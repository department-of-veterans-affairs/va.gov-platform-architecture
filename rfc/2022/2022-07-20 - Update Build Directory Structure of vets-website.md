# RFC: Update Build Directory Structure of vets-website

<!--
The title is what you want comments on. Use the active voice in a future tense.
Example:
    - The website will be built using the XZY framework
-->

* Comment Deadline: `2022-07-20`
* Team Crew and Name: Platform/Release Tools
* Authors:
  * [theodur](https://github.com/theodur)
* Related issues:
  * https://github.com/department-of-veterans-affairs/va.gov-team/issues/34687

## Background

Given the current structure of the `build` directory in `vets-website`, there are some challenges around implementing continuous production deployments for isolated applications. The `build` directory is created from building the applications that live in `vets-website`. `build` contains a subdirectory called `generated`, that stores the application bundles along with global bundles and assets created from Webpack, e.g. `facilities.entry.js`, `vaos.entry.js`, `web-components.entry.js`, etc. Applications are built in CI, where the `build` directory is uploaded to the `vetsgov-website-builds-s3-upload` bucket, and eventually synced to the `{ENV}-va-gov-assets` buckets for the respective environment's deployment. In the S3 deployment buckets, the `build/generated` directory is where bundles and assets are referenced from in the HTML pages on the site.
The problem with the current structure of the `build` directory, specifically the `build/generated` directory, is that all application bundles are stored alongside each other, along with global bundles, meaning the application bundles depend on the same global bundles. This isn't a problem when full builds are the only type of builds being deployed, but it becomes problematic when isolated application builds (single/grouped app builds) are also being deployed. 

Both full builds and isolated application builds generate their own assets and bundles each time they run. Full builds generate global assets and bundles, including bundles of all applications in the repo. Isolated application builds generate global assets and bundles as well; the main difference being they only generate application bundles that are specific to the application(s) being built. The current implementation of dev and staging deployments for isolated applications merely syncs the bundles of the built application(s) to the S3 deployment buckets, leaving global bundles and other application bundles untouched. This means that for VAOS application deployments for example, only application specific bundles like `vaos.entry.js`, `vaos.css`, etc. are synced to the S3 deployment buckets. The problem with this is there's no guarantee that the synced application bundles will work properly with the global bundles and assets that are live in the S3 buckets. The global bundles and assets that are built and used in isolated application builds can differ from the ones that were deployed in the last full build deployment. It's also difficult to perform automated QA on the application bundles from isolated builds with the global bundles and assets that are in live in the S3 buckets.

Storing isolated application bundles with bundles and assets from full builds also contributes to a race condition issue between continuous production deployments of isolated applications and daily production deployments. In the planned implementation of continuous production deployments, isolated applications will be deployed to production each time a commit with changes to the application(s) is merged into the `main` branch. If a branch with changes to an isolated application is merged into `main` during the time of the daily production deployment, the continuous production deployment triggered by the merge could end up writing to the isolated application's bundles in the S3 bucket at the same time as the daily production deploy, assuming the daily production deploy continued building and deploying all applications. Even if isolated applications were excluded from the daily production deployment, based on the current directory structure, changes to the global bundles and assets in the daily production deploy could break the untouched isolated application bundles in the S3 deployment buckets, inversely creating the same problem mentioned earlier.

Separating bundles and assets of isolated application builds from bundles and assets of full builds can prevent potentially breaking isolated applications in production.

## Motivation

Storing the bundles and assets of both build types separately would resolve issues that can occur from syncing isolated application bundles with global bundles and assets generated from previous deployments.

## Design

### Updating the structure of the `build` directory

Restructuring the `build/generated` directory involves updating the `vets-website` build to not store bundles of isolated applications in the root of the directory. Doing so means that the root directory will only contain bundles of applications that aren't isolated, along with the global bundles and assets generated from full builds, or in this case, builds of non-isolated applications. Bundles and assets from isolated application builds will be stored in their own subdirectory within the `build/generated` directory, e.g. `build/generated/check-in`, `build/generated/vaos`, etc. Doing this will ensure two things:

1. Bundles and assets from isolated application builds will be isolated from other builds.
2. Isolated application builds can be deployed independently of *full builds* (non-isolated application builds).

Updating the `build/generated` directory to follow this structure means that *full builds* will need to exclude the entry files of isolated applications, which ultimately excludes their bundles from being generated. The Webpack config will need to be updated to account for this, along with knowing when and where to store bundles and assets from isolated application builds.

### Updating the content build for the new directory structure

The `content-build` repository is responsible for creating the HTML files that are served on the site. By default, the build process in the `content-build` repository uses the `build/generated` path when injecting script tags that reference bundles and assets from `vets-website`. This will need to be modified, since assets could be loaded from `build/generated` or one of the subdirectories for isolated application builds, e.g. `build/generated/vaos`. To better determine which path in the deployment buckets to load bundles and assets from on each page, a generic script that handles this logic can be created in `vets-website`. The build process in the `content-build` repository can then be updated to inject that generic script on each page. This essentially transfers control of determining which assets get loaded to `vets-website`, making it unlikely to break production for applications migrating to the isolated structure.


### Updating CI for the new directory structure

Since there will no longer be a true *full build* once isolated application bundles are stored in their own subdirectory, we'll need to modify the process of building and testing global changes in CI. It won't be possible to build and test every application in one build anymore. One solution that would allow us to build and test multiple builds simultaneously involves Yarn Workspaces. With Yarn workspaces, each isolated application will have its own `package.json` file in the root of its directory, e.g. `src/applications/vaos/package.json`. The `package.json` files can be used to run the commands for each directory in `src/applications` that contains a `package.json`. This means we can use a command like `yarn workspaces foreach` at the root level to run commands like `build`, `test:unit`, `cy:run` (run Cypress tests), etc. The `workspaces foreach` command also has a `--parallel` option that can run the commands in parallel.

## Risks

One risk of this approach is that site wide elements rendered on pages of isolated applications can potentially get out of sync with site wide elements on non-isolated application pages. One example is if the header for the site has a major update that gets released in the daily production deploy. Since isolated applications would be excluded from the daily production deployment, the header update would not be applied to them at the time of the daily deployment, meaning some applications on the site would be using the new header, while others would be using the old header. This isn't ideal for consistency of the site, as we want all pages on the site to use the same site wide elements.

## Alternatives

An alternative approach to the outlined solution is to continue storing isolated application bundles in the `build/generated` directory with other application bundles, and test isolated application bundles with the global bundles that are in the deployment buckets before deploying isolated applications. This would require downloading the bundles and assets from the deployment buckets, syncing the recently built isolated application bundles with the downloaded assets, and run the application's Cypress tests to validate functionality in CI. Downloading the assets would only need to happen in the `main` branch, since other branches aren't deployed to any environments other than review instances. This means that in feature branches with isolated application changes, Cypress tests would run against the assets from the isolated application build as they currently do. If Cypress tests for isolated applications succeed in feature branches but fail when merged to `main`, there's a good chance that the global assets downloaded from the bucket are out of sync with the current state of `main`. This means isolated applications won't be able to be continuously deployed until the next daily production deployment happens. This isn't ideal because isolated applications also won't be deployed to staging until the next full build commit is merged into `main`.

The approach of storing isolated application bundles with full build bundles and assets wasn't chosen because it complicates CI/CD. We'd need to work around the constraint of validating functionality of the synced assets. Even if Cypress tests succeed after syncing the bundles and assets, we would need to determine a process for handling when Cypress tests do fail for the synced assets, since there wouldn't be anything that could be done until the global assets on `main` are in sync with the assets in the deployment buckets. It's not clear what that process would look like, and would probably be convoluted, if at all possible.
