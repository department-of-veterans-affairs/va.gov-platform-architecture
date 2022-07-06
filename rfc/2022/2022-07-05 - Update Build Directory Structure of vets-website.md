# RFC: Update Build Directory Structure of vets-website

<!--
The title is what you want comments on. Use the active voice in a future tense.
Example:
    - The website will be built using the XZY framework
-->

* Comment Deadline: `YYYY-MM-DD`
* Team Crew and Name: Platform/Release Tools
* Authors:
  * [theodur](https://github.com/theodur)
* [Original RFC Pull Request](https://github.com/department-of-veterans-affairs/va.gov-platform-arch/pull/-1)

## Background

Implementing continuous production deployments for isolated applications in `vets-website` with the current structure of the `build` directory presents some challenges. The `build` directory is the directory that gets created from building the applications in `vets-website`. `build` contains a subdirectory called `generated`, that stores the application bundles along with global bundles created from Webpack, e.g. `facilities.entry.js`, `vaos.entry.js`, `web-components.entry.js`, etc. The `build/generated` directory is where bundles and assets are referenced from on pages on the site.
The problem with the current structure of `build`, specifically the `build/generated` directory, is that all application assets are stored alongside each other, meaning they reference the same global bundles. This isn't a problem when full builds are the only type of builds being deployed, but it becomes problematic when isolated application builds (single/group app builds) are also being deployed. 

Both full builds and isolated application builds generate their own assets and bundles each time they run. Full builds generate global assets and bundles, including bundles of all applications in the repo. Isolated application builds generate global assets and bundles also, the main difference being they only generate application bundles that are specific to the application(s) being built. The current implementation of dev and staging deployments for isolated applications merely syncs the bundles of the built application(s) to the S3 buckets, leaving global bundles and other application bundles untouched. The problem with this is there's no guarantee that the synced application bundles will work properly with the global bundles and assets that are live in the S3 buckets. The global bundles and assets that are built and used in isolated application builds can differ from the ones that were deployed in the last full build deployment. Its also difficult and inefficient to perform automated QA on the application bundles from isolated builds with the global bundles and assets that are in live in the S3 buckets.

Having isolated application bundles stored with the bundles and assets from full builds also contributes to race condition issues with continuous production deployments for isolated applications and the daily production deployment. In the planned implementation of continuous production deployments, isolated applications will complete a production deployment each time a commit with changes to the application(s) is merged into the `main` branch. If a branch with changes to an isolated application is merged into `main` at the same time of the daily production deployment, the continuous production deployment triggered by the merge could end up writing to the isolated application's bundles in the S3 bucket at the same time as the daily production deploy, assuming the daily production deploy continued building and deploying all applications. If isolated applications were excluded from the daily production deployment with the current directory structure, changes to the global bundles and assets in the daily production deploy could break the untouched isolated application bundles in the S3 bucket, inveresely creating the same problem mentioned above.

Seperating the bundles and assets from isolated application builds and full builds can prevent potentially breaking isolated applications in production.

## Motivation

Storing the bundles and assets of both build types seperately would resolve issues that can occur from syncing isolated application bundles with global bundles and assets generated from previous deployments.

## Design

Restructuring the `build/generated` directory involves removing bundles of isolated applications from the root of the directory. Doing so means that the root directory will only contain bundles of applications that aren't isolated, along with the global bundles and assets generated from those full builds, or in this case, builds of non-isolated applications. The bundles and assets of each isolated application will be stored in their own subdirectory within the `build/generated` directory, i.e. `build/generated/check-in`, `build/generated/vaos`, etc. Doing this will ensure two things:

1. Bundles and assets from isolated application builds will be isolated from other builds.
2. Isolated application builds can be deployed independently from the *full build*.

Updating the `build/generated` directory to have use structure means that *full builds* will need to exclude the entry files of isolated applications. The Webpack config will need to be updated to account for this, along with knowing when and where to store the bundles and assets from isolated application builds.

Creating subdirectories for isolated application builds creates a different set of challenges:

- Updating the `content-build` repository to account for this change
- Updating the CI workflow for global changes in `vets-website` that require QA on each application in the repository

The `content-build` repository is responsible for creating the HTML files that are served on the site. The build process of the `content-build` uses the `build/generated` bucket by default when injecting script tags that reference the bundles and assets from `vets-website`. This will need to be modified, since assets can be loaded from `build/generated` or one of the isolated application subdirectories, e.g. `build/generated/vaos`. To better determine the path in the S3 bucket of the bundles and assets to load on each page, a generic script for determining which assets get loaded on each page can be created in `vets-website`. The `content-build`'s build process can be updated to inject the generic script on each page. This essentially transfers control of determining which assets get loaded to `vets-website`, making it unlikely to break production for applications migrating to the isolated structure.

Since there will no longer be a true *full build* once the isolated application bundles are stored in their own seperate subdirectory, this means build and testing global changes in CI will need to modified. We won't be able to build and test every application in one build anymore. One solution that would allow us to build and test multiple builds simultaneously involves Yarn Workspaces. With Yarn workspaces, each isolated application will have it's own `package.json` file in the root of it's directory, e.g. `src/applications/vaos/package.json`. We can utilize the `package.json` files to run the commands for each directory in `src/applications` that contains a `package.json`. This means we can use a command like `yarn workspaces foreach` at the root level to run commands like `build`, `test:unit`, `cy:run` (run Cypress tests), etc. The `workspaces foreach` command also has a `--parallel` option that can run the commands in in parallel.

## Risks

One risk of this approach is that site wide elements rendered in isolated application pages on the site can potentially get out of sync with site wide elements rendered on non-isolated application pages. One example is if the header for the site has a big update that gets released in the daily production deploy. Since isolated applications would be excluded from the daily production deploy, the header update would not be applied to them, meaning some applications on the site would be using the new header, while others would be using the old header. This isn't ideal for consistency of the site, as we want all pages on the site to use the same site wide elements.

## Alternatives

List the alternative approaches

* There are always alternatives. What other alternative solutions were considered? Not considered?
* What are their strengths, weaknesses, risks? Why weren’t they chosen?
* Do not allow bias of a solution to show in this section, ensure each alternative has been considering seriously or do not list it in this section

## References

<!--
This section /may/ be eliminated if it is not applicable.
When linking to other documents in this repository, ensure to link to their state at a particular commit (hence the usage of .../blob/<FULL_COMMIT_HASH>/...) as opposed to their current state (i.e. 'currently in main').
-->

* [RFC XYZ](https://github.com/department-of-veterans-affairs/va.gov-platform-arch/blob/<FULL_COMMIT_HASH>/rfc/<FILENAME>.md): <RFC_TITLE>


