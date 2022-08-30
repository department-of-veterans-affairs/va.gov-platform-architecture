# RFC: Platform-wide VFS developer documentation will be viewed on the VA Platform Website; component-specific technical documentation will be viewed on the Console UI

<!--
The title is what you want comments on. Use the active voice in a future tense.
Example:
    - The website will be built using the XZY framework
-->

* Comment Deadline: `2022-08-31`
* Team Crew and Name: [Platform/Spike Teams/Console UI](https://github.com/orgs/department-of-veterans-affairs/teams/platform-pst-console-ui)
* Authors:
  * [Thomas Ferrin](https://github.com/TPurple)
  * [John Chapin](https://github.com/jchapin)
  * [Susanna Kline](https://github.com/sdk-irl)
* [Original RFC Pull Request](https://github.com/department-of-veterans-affairs/va.gov-platform-architecture/pull/33)

## Background

The Platform currently has at least three locations for documentation:
* The (Platform website)[https://depo-platform-documentation.scrollhelp.site/index.html], which uses (Scroll Viewport)[https://www.k15t.com/software/scroll-viewport/scroll-viewport-for-confluence-cloud] to display specific content hosted in Confluence. This website hosts the following types of content:
    * **“Getting started”** content for onboarding VFS team members to work on the Platform
    * **“Developer docs”** content about writing and submitting code for review
    * **“Research and design”** content for adhering to the Platform’s guidance for research and design
    * **“Analytics and insights”** content for analytics, KPI dashboards, and monitoring
    * **“Collaboration cycle”** documentation of the process that VFS teams are required to follow to ensure quality for all products and features launched on [va.gov](http://va.gov/).
    * **“Support”** policies and incident management
    * **“User guides, how-to instructions, tutorials, or reference documents for VFS teams”** ((source)[https://vfs.atlassian.net/wiki/spaces/CUI/pages/2186543112/RFC+DRAFT+Documentation+Locations+-+Console+UI])
* (Operational documentation)[https://vfs.atlassian.net/wiki/spaces/AP/pages/1287782439] throughout Confluence, which includes internal content such as “(meeting notes, roles and responsibilities documentation, decision documentation, [and] initiative outlines.)[https://vfs.atlassian.net/wiki/spaces/AP/pages/1287782439]”
* The [design.va.gov](https://design.va.gov) site contains the content style guide, components, and patterns that include guidance, standards, and tools to help teams build a consistent, intuitive, and Veteran-centered experience across VA.gov.

While Confluence and the Platform website contain helpful documentation for VFS (and Platform) teams, a lot of documentation—specifically technical documentation for a software component, service, or infrastructure resource—is found throughout various GitHub repositories. This documentation is vital to developers trying to troubleshoot or set up software dependencies, but it is much harder to find. Developers typically find it by asking around, receiving a few different answers, and sometimes finding different versions of the documentation. When they find the documentation, it is often difficult to know whether it is complete or up to date. Additionally, some VFS teams rely on systems external to the VA and have had difficulty finding or maintaining documentation about these systems.

## Motivation

If we can make it easy for developers to locate and store technical documentation, we can save them hours (or days) of extra work. In addition, having a consistent method and location for finding this documentation is a crucial part of improving the experience and efficiency of VFS teams.

## Design

We propose that the central location for viewing component-specific technical documentation—including APIs, software components, and infrastructure—should be the Console catalog. The catalog makes it easy for users to navigate an org chart-type view of the VA ecosystem (including VFS apps, Platform services, and user groups) and quickly find documentation about any part of it.

Resources like The Platform Website, Confluence, and design.va.gov are utilized by a broad audience, including VFS Teams, Platform Teams, and other VA Platform Stakeholders. These sites also fulfill domain-specific missions:

* teaching developers about the VA Platform at large
* recording operational information
* teaching designers how to build standards-compliant user experiences 

For those reasons, we don't think it is appropriate to move their contents inside the Console UI, but we believe there will be content and links that frequently connect these resources.

Using the Console catalog to solve the problems outlined above will necessarily also include the following:

1. **A clear definition of what counts as “component-specific technical documentation.”** This definition will help teams know what documentation should be stored in the Console catalog versus on the Platform Website or in Confluence.
    1. Documentation that should live in the Console catalog is:
        1. specific to a software component, API/service, or piece of infrastructure
        1. technical information needed for development
        1. intended for use by only a subset of VA teams or roles
    1. Examples include:
        1. Documentation that is internal to a VFS team
        1. Highly technical documentation about an infrastructure resource
        1. Documentation for VA APIs and downstream external services
1. **A clear relationship between the Platform Console and the Platform Website.** We propose that the Console and Website contain numerous references to each other. For example, if a component in the Console catalog has documentation that is relevant to all VFS users, it should have a link to Platform Website documentation on the topic. If the Platform Website has a page that mentions technical documentation that not all VFS teams need, it should link to the relevant documentation in the Console catalog. 
1. **Storing documentation close to the software project/component.** Documentation for a component should be stored in the same repo as the component (where possible) or in a central documentation location and then ingested into the Console catalog, so it is easy to find.
1. **A self-service process for adding documentation.** Teams who own software components, services, and resources will register these entities in the Console catalog. They will include references to their technical documentation and spell out both upstream and downstream dependencies. They may also create catalog entries for dependencies (and their documentation) that are external to the VA.
    1. Teams would create a ```/docs``` folder at the root of their project to contain markdown files and subfolders for organizing their documentation.
    1. Inside the ```/docs``` folder, a mandatory index.md file acts as the landing page for documentation in the TechDocs plugin. It may contain content similar to the README of a project, or it may be a more comprehensive document explaining the project and its documentation.
    1. Outside of the ```/docs``` folder, in the project's root folder, there will need to be a manifest file ```/mkdocs.yml``` that controls the nesting/rendering of navigation inside of TechDocs.
    1. A documentation annotation will also be required in the catalog-info.yaml file. This annotation indicates to Backstage where the technical documentation for a catalog entry can be found.
    1. Documentation for TechDocs can be found here: (TechDocs "HOW TO" guides)[https://backstage.io/docs/features/techdocs/how-to-guides]. It is possible to store TechDocs outside of the source code repository, and we have considered that there could be benefits to using GitHub Wiki repositories to make editing documentation more convenient.

## Potential Benefits

Possible benefits of a centralized system for component-specific technical documentation include these:

* VFS and Platform teams will more easily maintain documentation about their software projects for themselves or others to reference later. The TechDocs plugin within Backstage contains built-in links to help maintainers edit documentation. Readers can also highlight documentation and open issues for questions or corrections directly from the reading interface.
* VFS teams will find it easier to document information about downstream systems external to the VA. They can either create documentation collections for these systems or import the documentation directly from these teams if they already exist.
* New VFS and Platform team members will more easily be able to learn about the software projects that their team maintains.
* VFS and Platform team members can easily find and discover documentation about any VFS and Platform entity. The Console UI has interfaces for searching through all projects with documentation across the entire catalog. Each collection of documentation also has a search interface that will confine searches to specific projects or assets.
* Each component’s source code and documentation will be easier to find and less likely to be lost than if they were stored in diverse locations.
* With connections between component and their documentation better maintained, it will be easier to care for each software component, its documentation, and systems that depend upon the component.
* With each component's documentation stored close to its source code, it will be easier to run automated checks to see if each component's documentation is up to date. Keeping the documentation with the source code also ensures that we're following VA policy to make all documentation publicly accessible.
* VFS and Platform team members will have confidence in their access to a single source of truth, ensuring that engineers have access to the most relevant, up-to-date information.

## Risks

* A risk of this strategy is that Platform-wide documentation intended for all VFS developers could be mistakenly added to the Console instead of the Platform Website, making it harder to find. Platform and VFS Engineers will need clear instructions to avoid this.
* If the Console UI system is retired, all dispersed component-specific documentation loses its central location for being found. This scenario would be a sub-optimal situation but still better than storing documentation in a central system separate from the source code of assets. Elimination of such a system may permanently separate software from the documentation.

## Alternatives

* We could drive people to add documentation for their components/projects to the Platform Website. The risk associated with this alternative is that documentation may become separated from source code and add irrelevant documentation to some VFS developer audiences.
* We could encourage developers to use a system like Confluence for all component-specific technical documentation. But that may incur additional licensing costs and the possibility that all documentation is lost if Confluence, or a similar system, is retired. Also, VFS team members do not always have access to Confluence.

**Questions from Platform Content team to consider:**

- Is there a high risk that documentation ends up on both the Platform and the Console simultaneously?

- Is there any component-specific documentation on the Platform website currently, which would need to be migrated to the Console? 

- Will there be any documents with both component-specific content and more general content? If so, how will we know where to place that? Will we have to separate out such content into separate docs?

- Who is currently defining the IA of the Console and who will define it in the future?

- Feedback about this write up: “Collaboration Cycle” …add a definition
