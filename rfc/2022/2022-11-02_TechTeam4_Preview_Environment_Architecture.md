# RFC: The Preview environment architecture will generate publicly accessible environments hosted in EKS using mocked test data.

* Comment Deadline: `2022-12-15`
* Team Crew and Name: Platform/Tech Team 4
* Authors:
  * [pjhill](https://github.com/pjhill)
  * [JoeTice](https://github.com/JoeTice)
* [Original RFC Pull Request](https://github.com/department-of-veterans-affairs/va.gov-platform-arch/pull/-1)

## Background
The current version of ephemeral environments for use on the VA.gov platform is referred to as Review Instances. Review instances provide many capabilities, but several characteristics of review instances cause pain points.
1. Review instance creation operates under the deprecated BRD (build, release, deploy) paradigm
2. VFS team members without SOCKS access or the technical knowledge to use SOCKS can't use review instances
3. VA stakeholders without SOCKS access or the technical knowledge to use SOCKS can't use review instances
4. VFS team members are unable to reach review instances with tools hosted in the cloud
5. VFS team members are unable to preview changes from multiple sources in a single preview environment
6. Review Instances don't always spin up reliably
7. Review Instances aren't available when needed, and the timeframe can't be adjusted
8. VFS Teams are unclear on the benefits and intended use of Review Instances
9. Review Instances can't be tested on real or virtual mobile devices

As a result, review instance usage is fairly low. As of 11/14/2022, less than 10% of the review instances that are automatically created are ever accessed by a user.

## Motivation

The preview environment architecture change is intended to update the creation of preview environments to the new EKS deployment strategy, reduce or remove the impact of the listed pain points, and increase VFS team use of the ephemeral environments that are created.

The EKS hosting paradigm for ephemeral environments enables preview environments to spin up more quickly, produce more consistent user experiences upon interaction with the environments, and enable greater customizability for each environment. Additionally, moving this platform process to the new EKS paradigm reduces the breadth and complexity of the infrastructure code that Platform needs to maintain in order to operate successfully.

## Design
TBD

<!--Explain the proposed design in enough detail so that a team member will fully understand the implementation. Include a diagram (in the `images` dir) as needed to convey your plans. Use active voice, present tense, and decisive language. -->

## Risks
TBD

<!--
List the risks of this approach

* There are always risks. What are the risks of this solution?
* These are the things people will bring up in opposition to your idea or plans. Acknowledge them.
-->
## Alternatives
* Custom preview environment build process driven by events in GitHub, built by GitHub Actions, and orchestrated by EKS.
* GitHub Codespaces : Codespaces is billed by GitHub as a "dev environment in the cloud." While Codespaces does create development environments, the primary purpose of these development environments is focused around coding and controlling the creation of reproducible development environments for the purposes of collaboration between developers and the facilitation of onboarding new developers.
  * Supported aspects of Platform use cases:
    * Access control for environments
    * Ability to make an environment public
    * Removes reliance on BRD infrastructure paradigm
    * Removes requirement for SOCKS in order to access the environment
    * Ability to define specific versions of additional resources
  * Unsupported aspects of Platform use cases:
    * Unable to integrate with VA enterprise lower environments
    * Unable to directly promote a development environment
    * Unable to orchestrate deployments with EKS
* TugboatQA : Data-first testing and review environments within a mighty serverless platform. The CMS team operates an "on-prem" version of Tugboat. This means that Tugboat environments have easy access to other VA enterprise resources. Tugboat is a product built specifically for creating ephemeral environments for the purpose of testing and demoing .
  * Supported aspects of Platform use cases:
    * Able to integrate with VA lower environments
    * Removes reliance on BRD infrastructure paradigm
    * Ability to customize the preview environment
    * Support for mocking integrations / test data
    * Ability to use different version of different resources
  * Unsupported aspects of Platform use cases:
    * Current on-prem solution requries SOCKS access
    * Unable to directly promote a development environment
    * Unable to orchestrate deployments with EKS

## Diagrams

Try to visually represent the proposal using a [diagram](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-diagrams). [Live editor](https://mermaid.live/)
```mermaid
flowchart LR;
    A[Developer creates/modifies a pull request] --> B{CI workflow passes}
    B --> |Yes| C[Dev has settings stored]
    B --> |Yes| D[Default settings]
    B --> |No| Z[No preview environment NOT generated]
    C --> E[Retrieve settings stored in DevOps repo file]
    E --> G[Preview Environment deploy command]
    G --> |Mocked Data| H(PE Created in dev)
    H --> J[Preview environment accessible]
    J --> K[Preview environment evaluated by users]
    K --> L{Are updates necessary?}
    L --> |Yes| A
```

## References

<!--
This section /may/ be eliminated if it is not applicable.
When linking to other documents in this repository, ensure to link to their state at a particular commit (hence the usage of .../blob/<FULL_COMMIT_HASH>/...) as opposed to their current state (i.e. 'currently in main').
-->

* [RFC XYZ](https://github.com/department-of-veterans-affairs/va.gov-platform-arch/blob/<FULL_COMMIT_HASH>/rfc/<FILENAME>.md): <RFC_TITLE>


