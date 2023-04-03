# RFC: Content Management System Will Migrate from BRD to Lagoon with a Paid Support Contract

<!--
The title is what you want comments on. Use the active voice in a future tense.
Example:
    - The website will be built using the XZY framework
-->

* Comment Deadline: `2023-05-01`
* Team Crew and Name: Sitewide/CMS-Platform
* Authors:
  * [Dawn Pruitt](https://github.com/dawnpruitt)
  * [Eric Oliver](https://github.com/olivereri)
  * [Elijah Lynn](https://github.com/ElijahLynn)
  * [Erika Washburn - Prior to OCTO-DE Transition](https://github.com/EWashb)
  * [Nate Douglas](https://github.com/ndouglas)

## Background

VA.gov-CMS currently uses an inner-source Jenkins & Ansible-based Build-Release-Deploy (BRD) system developed by various teams on the Veteran-Facing Services Platform (VSP) contract. This is a set of processes, tools, and workflows that are used to manage the development, testing, and deployment of software applications.

The BRD system is responsible for installing package dependencies, running automated tests, creating deployable artifacts (Amazon Machine Images (AMIs) or single Docker containers), and deploying those artifacts to DEV, STAGING and PROD environments. The CMS uses the AMI approach on BRD. 

The BRD system is based on a collection of Jenkins jobs which in turn call Ansible playbooks. The CMS needs a modern container deployment solution to enable better testing, debugging and faster deployments. 

VSP teams have been working to deprecate the BRD system since 2021. We must identify an optimal delivery platform to replace BRD, prior to its deprecation, so that we can sustain reliable delivery of the VA.gov-CMS application and its content even as BRD is decommissioned.


## Motivation

Our team's technical discovery process evaluated several options for replacing the current system. We identified Lagoon, a 100% open-source web application delivery platform developed by Amazee.io, as the best solution.

To confirm its viability, a proof of concept was conducted to implement Lagoon in a demo environment using their Platform as a Service (PaaS) offering. The results were successful, demonstrating that Lagoon can scale, support continuous deployment, and reduce upfront implementation and maintenance time.

However, the PaaS version of Lagoon is not FedRAMP certified, making it unsuitable for our specific security and compliance requirements. Fortunately, Lagoon can be installed in our AWS GovCloud account, making it a feasible solution.

Lagoon is entirely open-source and comes with on-site support contracts that provide 24/7 Slack and Phone support. This ensures that technical and community support are available to assist with any issues that arise during implementation and maintenance.

Lagoon solves several problems associated with the current Jenkins-based Build-Release-Deploy (BRD) system:

Lagoon is purpose-built for both Drupal and general containers, providing greater scalability and flexibility compared to the current BRD system. This allows Lagoon to meet the unique needs of different applications and workloads, which can help streamline software development processes and reduce implementation time.

Lagoon provides technical and community support for continuous deployment and new features, which can help ensure that the system stays up-to-date and meets the evolving needs of the organization.

Lagoon reduces upfront implementation time and ongoing maintenance time after implementation. Because Lagoon is purpose-built for applications like Drupal, there is less custom logic that our team needs to research, design, and implement ourselves. Ultimately, this reduces risk by a considerable amount.

As a result, Lagoon can help reduce errors, delays, and costs associated with software development, leading to more efficient and effective delivery of applications and content.

The Lagoon model strikes a balance between building everything as custom code and paying for a fully managed platform. By taking advantage of community-sourced work and having a support contract, we, the client, and the public get the benefits of open-source software while also having access to expert support and a roadmap for ongoing improvements. This reduces the burden on our DevOps team while also providing a cost-effective and flexible solution. Should the Department of  Veterans Affairs choose another contractor in the future, this will ease the transition for all involved.

Amazee is a very mature and respected service provider in the space; they’ve been hosting enterprise websites for over ten years, and, as they put it, Drupal is “their first love.”  Lagoon was originally released in August of 2017, and is used for many enterprise and government websites, including over 400 Australian government websites.


## Design

Our plan is to deploy Lagoon in AWS EKS clusters and manage it directly. The system will include separate environments for development, staging, and production.

The CMS team will take full responsibility for the installation, configuration, and operation of Kubernetes clusters and Lagoon Remote installations. This includes the installation of a Lagoon Core service, which customer Lagoon Remote clusters can connect to.

Amazee.io will provide training, support, and a service level agreement for response times to help ensure the successful implementation and ongoing operation of Lagoon.

By managing the installation and configuration of Lagoon in-house, the CMS team can ensure that the system aligns with our specific needs and requirements, and can also take advantage of Lagoon's scalability, flexibility, and ease of use.


## Risks

There are some risks associated with moving to Lagoon. In some cases, these are risks particular to the nature of Lagoon, or its vendor, or that it runs on Kubernetes. Others are inherent to any specific replacement for Build-Release-Deploy, or even remaining on BRD.

### Dependency on a single vendor:
**Risk**: Lagoon is an open-source solution, but our support contract will be with Amazee.io. This means we will be reliant on a single vendor for support, which could become a problem if they go out of business or decide to discontinue support for Lagoon.

**Mitigation**: While Amazee is the sole vendor of Lagoon, we do anticipate that the product will be adopted at a sustainable volume. The DevOps team also will develop some degree of internal expertise in Lagoon, enabling us to maintain and troubleshoot the platform independently as needed. If worse comes to worst, we believe that our knowledge and experience running Drupal on multiple platforms will enable us to transition smoothly away from Lagoon.

### Limited control over the platform:
**Risk**: While Lagoon provides a lot of flexibility, there may be certain aspects of the platform that we cannot control. This could limit our ability to make customizations or modifications to meet our specific needs.

**Mitigation**: We have clearly defined our requirements and expectations with Amazee, and they have been amenable. Lagoon was designed with Drupal in mind, so the amount of adjustment that needs to be made by either side is comparatively minor. And, of course, Lagoon is open source, so the broader user community exerts some power; well-engineered, zero-cost solutions that we develop should be minimally disruptive and probably will not encounter much friction. And if a feature really needs to be implemented that would not be accepted upstream, then it is possible since it is open source, similar to custom code.

### Learning curve and onboarding time:
**Risk**: Moving to a new platform always requires some learning curve and onboarding time. There may be a period of adjustment as our DevOps team gets up to speed with Lagoon and its specific workflows and processes. Lagoon will run directly on Kubernetes, which requires some adjustment and learning as well.

**Mitigation**: Amazee provides three kinds of training for new engagements, centered on Lagoon Core, Lagoon Remote, and application development. In addition, monitoring and alerting are covered in an additional workshop. This training is included in the contract and should involve all relevant team members.

It becomes our responsibility to maintain this knowledge and extend it to new team members; this is of course not trivial, but we already do this sort of documentation and training (mostly documentation) for new hires with the Tugboat and Build-Release-Deploy (BRD) systems.

### Potential for compatibility issues:
**Risk**: There is a risk of compatibility issues with other tools or systems we use. This could cause delays or even failures in the deployment process.

**Mitigation**: Our Drupal codebase as it currently stands is fairly conventional. Often, a Drupal project will find itself accumulating additional custom applications that are bolted onto the core, reusing the database, reusing the filesystem, etc. Ours is not really like that. 

We do have a number of critical upstream and downstream relationships with other applications and systems, but these are well-documented and well-understood. We’ve also gone to pains to keep our dependencies updated and our code following best practices.

Amazee is also experienced with Drupal and the process of migrating Drupal codebases to run on Lagoon. We expect that our combined resources will minimize the risk and degree of any issues. Amazee and Lagoon have been around for about six years at this point, and Lagoon is (among other things) managing over 400 sites for the Australian government, so they have more than adequate experience.

### Complexity of Initial Setup:
**Risk**: As Lagoon is a new system with a different architecture, it may require significant effort and expertise to properly set up and configure. This could result in delays or unexpected costs if unforeseen issues arise during implementation. Additionally, if the CMS team is not properly trained on Lagoon and its components, it may take longer to get up to speed and become fully comfortable with the system, which could lead to additional errors or inefficiencies during the transition period.

**Mitigation**: As mentioned above, Amazee has standardized training in place. Also, the migration process is coordinated with both teams; it is not a do-it-yourself, trial-by-fire migration. There are no looming crises, or hard deadlines; we expect that the migration will complete while the BRD system is still officially operational and supported.

Amazee is also going to help with adapting Lagoon to our existing architecture, and will be participating in a shared Slack channel and Zoom calls for initial setup. 

The majority of engineers do not need to be well-versed in Lagoon to do their jobs, any more than they need to understand Jenkins or Ansible to do so currently. There are no new requirements for design or code.

### Ongoing Maintenance:
**Risk**: Kubernetes can (and has, in the past) present unexpected difficulties in the update process. Currently, the Platform EKS clusters appear to be running on the oldest supported version, 1.22, for which official support will be discontinued in less than three months. The DevOps team may be caught in the middle of the preferred Lagoon development configuration and the current configuration provided by Platform Ops, having to deal with both to maintain stability.
**Mitigation**: There’s no royal road to Kubernetes. The DevOps team will need to deepen its familiarity with Kubernetes and be proactive about identifying and addressing possible compatibility issues. We will need to keep abreast of new developments, liaise with both Amazee and Platform Ops, and be assertive about our needs and concerns.

### Logging Visibility:
**Risk**: The DevOps team takes pride in its maintenance and development of the CMS product’s infrastructure and deployment architecture. That is made possible by the team's unmediated access to all environments, tools, etc. Lagoon, existing first and foremost as a Product-as-a-Service, is not completely transparent. One of these issues is that logs are not visible in the Lagoon UI; rather, we need to dive into individual service and pod logs.

**Mitigation**: We have raised this concern with Amazee, and the support contract in its current form contains dedicated provisions for improving this aspect of the platform.

## Alternatives

The DevOps team has been aware of the Build-Release-Deploy system’s looming deprecation for some time. We’ve considered a number of options and have concluded that Lagoon seems to balance our concerns the best.
In evaluating these alternatives, we focused on a few questions:

> Does the system have a track record of reliability and uptime? How difficult is it to maintain the system’s stability and recover from failure?
> 
> Does it offer the necessary scalability to handle peak demand (e.g. from content-build) and our anticipated future growth?
> 
> Does it meet all of our security and compliance requirements?
> 
> Does it have a strong support and maintenance model, including access to technical support and troubleshooting assistance?
> 
> Is it cost-effective and within budget constraints?
> 
> How easy is it to adopt, given our current infrastructure and requirements?
> 
> How much control do we have over deployment processes and configuration?
> 
> How easy is it to customize or extend functionality to meet new feature requirements as they arise?
> 
> What level of documentation and community support is available?
> 
> What compatibility issues can we anticipate? What challenges?

We will not be addressing each question individually for each alternative; rather, we’ll discuss the major points that contributed to our conclusions.

### Adopting the Build-Release-Deploy System
The BRD system is already in place, and does work. It costs nothing in terms of time or training for the DevOps team to simply take over. We are fairly well-versed in its intricacies and idiosyncrasies and can generally respond quickly when there is an issue. It works, and we can work with it. We have made changes as needed, for instance to upgrade from Amazon Linux to Amazon Linux 2, various PHP version upgrades, etc.

The current BRD system is a complex collection of several different technologies: HashiCorp's HCL in Terraform, Ansible, custom Python code in filters, Jinja templates, Jenkins Groovy scripts, Cloud-Init, scattered Bash scripts, ad-hoc use of the AWS CLI, etc.

The different syntaxes and behaviors of these languages and systems can make it difficult to understand the system as a whole, which can lead to errors, delays, and increased costs. This can make it more challenging to onboard new engineers and to keep the system running smoothly over time.

We also need to maintain Jenkins itself, which can be a significant undertaking. From time to time, Jenkins may need to be updated for security or other issues, which can create an additional maintenance burden that our small DevOps team would need to handle with little or no advance warning.

There is also substantial technical debt in the Ansible playbooks and roles. The Ansible artifacts were developed with generalization in mind, so that teams could "hook in" at various points to customize process behavior. As the only users of BRD, we would see this become a source of additional cost in time and effort for no measurable benefit. 

But refactoring or rewriting the codebase would also be daunting, given the sheer amount of testing that would entail and the expensive resources created and destroyed in the testing process.

The security team's scans often reveal that third-party applications we use in the BRD platform are configured slightly incorrectly or are of a version with known security vulnerabilities. That becomes an issue that we need to remediate as soon as possible, obstructing our ability to make forward progress on our other commitments.

In our experience, the number of security vulnerabilities seems to scale – as one might expect – roughly linearly with the number of different applications and packages we use. A more complex project, involving more distinct packages, will have more vulnerabilities and incur more firefighting instead of innovating.

In addition, updating or reconfiguring a single package can often have unforeseen consequences, such as the need to update or reconfigure other systems for compatibility. This can increase the overall testing and deployment workload, and can introduce new issues and risks into the system.

As it stands, we do not really have the ability to scale up or down easily. We have never tested horizontal scaling. We can vertically scale by changing instance sizes, but this is a fairly lengthy process. In practice, we anticipate that we might hit a red line for system load or some other metric and upsize to accommodate growth, or we see that the CMS-Test infrastructure can be downsized to save money with acceptable effects on our infrastructure testing and development.

In the end, we acknowledge that the BRD system poses the lowest short-term cost. We do not feel it is a viable alternative, though, given the long-term costs and risks associated with its maintenance.

### The Argo CD/EKS/GitHub Actions System
Another alternative to Lagoon and the current BRD system is to directly run Drupal in a customized deployment solution using Platform Ops' EKS clusters and Argo CD. This approach would give us complete control over the deployment process and allow us to tailor the solution specifically to our needs.

One of the biggest strengths of this approach is that it would give us direct access to the EKS clusters managed by Platform Ops, which would provide a stable and well-supported foundation for our deployment. Additionally, by using Argo CD, we would be able to manage and automate the deployment process, which could potentially reduce the amount of manual intervention required by the DevOps team.

However, there are also several potential weaknesses and risks associated with this approach. For example, developing and maintaining a custom deployment solution would require a significant investment of time and resources, which could divert attention away from other important priorities. Additionally, there would be a learning curve involved in getting up to speed with EKS clusters and Argo CD, which could slow down the deployment process and introduce new errors or inefficiencies.

There are also potential risks associated with running Drupal directly in a customized deployment solution. For example, the deployment process could become overly complex and difficult to manage, which could increase the likelihood of errors or downtime. Additionally, there would be a risk of compatibility issues with other tools or systems we use, which could cause delays or even failures in the deployment process.

The CMS team was not consulted prior to development of this system, but we are responsible for maintaining our application within it and extending the system to meet Drupal requirements where needed. That impedance mismatch can be unpleasantly large and costly. 

One example of this is the lifecycle of the Drupal deployment, in which the codebase is switched while the application is running, caches are cleared, updates are performed, and so forth. This is tricky and difficult to get right, and the specific hazards posed by a trivial update that adds a few lines of code somewhere will differ from those posed by a minor Drupal version update, or one where a module is uninstalled, or one where the configuration for a View is modified.

This solution also involves a lot of closed-source “glue” code; application manifests and so forth that need to be maintained within private repositories. Of course, there are secrets essential to our security model, but those are best managed elsewhere – not in a repository anywhere. Our team prefers to be as open as possible with our projects and our knowledge.

The number of different components in the base system, combined with the additional tooling we would need to create to build out the complete deployment lifecycle, leads to a situation where errors and failures may be exposed in several different places. It becomes very difficult to get “a single story” that details the journey of a piece of code into production, and even harder to determine why the application may not be behaving as desired. It’s likely that there will be issues that we’re simply not aware we’re having, due to fragmented observability or the complex interplay of permissions and access.

Ultimately, we decided against this approach for a few reasons. First, the amount of effort required to develop and maintain a custom deployment solution would inevitably be greater than the effort required to work with a pre-built solution like Lagoon. Second, using a pre-built solution like Lagoon would give us access to a larger community of developers and experts, which could provide additional support and resources.

We believe that any money and time saved by using a completely custom solution, such as we would develop here, would be spent in a protracted and painful project development process as we repeated every lesson learned by Amazee in the course of developing Lagoon. We would also need to write and rewrite documentation as the system evolved. We believe that the end result would be significantly less reliable, less well-documented, and take significantly longer to implement.

### A Fully Customized Solution Built on Nomad
Hashicorp Nomad is another potential alternative. Nomad is an open-source cluster scheduler that can be used to deploy and manage applications. It is similar to a less complex, monolithic version of Kubernetes.

This would give us a substantial amount of flexibility, probably greater than any other alternative. We would be able to define and manage the entire infrastructure using a single platform, which could reduce the complexity of our deployment process. 

Nomad is designed to be lightweight and easy to use, especially compared to Kubernetes, which could make it easier to onboard new engineers and to keep the system running over time.

Nomad is also configured in HCL, the same basic configuration language used by Terraform, with which the DevOps team has experience. Terraform is a common skill possessed by DevOps engineers, so even if Nomad itself is unfamiliar to a new engineer, at least the syntax should prove relatively familiar.

However, Nomad is less established than Kubernetes and has a smaller user base. Hashicorp, its principal developers, have occasionally made some concerning moves, e.g. ceasing work on community pull requests to Terraform because of low staffing in the core team. Despite Nomad’s strengths, its comparatively low adoption and dependence on Hashicorp indicate an uncertain future.

Furthermore, Nomad is not specifically designed with Drupal in mind, so any custom functionality that would need to be developed for running on the Argo CD platform would also need to be developed here, but we would not even be able to ask the Platform Ops team if we had questions. We would be wholly reliant upon Hashicorp and the Nomad community.

### Acquia
Acquia is a cloud-based Platform-as-a-Service (PaaS) that provides a complete hosting and deployment solution for Drupal and other web applications. It offers a number of features and benefits, such as automatic updates, monitoring, and backups, as well as expert support and consulting services. It has also recently been FedRamped.

Several members of the team have worked with Acquia-hosted systems in the past. A positive thing that can be said is that we feel like we know what to expect. On the other hand, the general sentiment toward Acquia based on those experiences is rather negative.

One of the biggest strengths of Acquia is that it provides a turnkey solution for hosting and deploying Drupal, which could potentially reduce the amount of time and effort required by our DevOps team. It also offers 24/7 support.

Acquia’s system is also simple and reliable. The offering with which we are most familiar is run directly on AWS EC2 servers. Deployment is very quick and almost always painless.

However, there are also several potential weaknesses and risks associated with this approach. For example, Acquia is a proprietary platform, which means that we would be reliant on a single vendor for support and maintenance. 

Acquia’s deployment is quick, but it is not instantaneous. A transition period of a few seconds exists in which any request will encounter a server error, and going by experience this will reliably and repeatedly lead to help desk tickets.

Our insight into and ability to troubleshoot the system is limited. Support will respond in accordance with their own perception of the problem, and the feedback cycle can be frustratingly long. From experience, the DevOps team might end up doing all of the hard work necessary to document that a problem exists and what exactly caused the problem before support engineers will acknowledge that it exists. In this sort of situation, there’s not actually much time saved by having their support team available.

Additionally, using a PaaS like Acquia could limit our ability to make customizations or modifications to meet our specific needs, which could impact the flexibility and scalability of our deployment. As an example, a team member requested installation and configuration of a specific Varnish module to address performance issues, and this was flatly denied.

Acquia does not yet support containers, which we would very much like to upgrade to. Containerizing our application makes it easier to “build once and deploy anywhere”, which reduces the burden of maintaining parity in package versions, features, and behavior across our environments. It also makes it easier to onboard new engineers, as it requires less training and less time to set up local development environments.

There are also substantial cost implications associated with using Acquia. The cost of using Acquia could be higher than with other alternatives. Acquia’s tiered offerings do not accommodate the kind of flexibility that our other options do: rather, we are charged for the tier that matches the magnitude of our most significant constraint, and all excess capacity just becomes costly waste.

## Diagrams

![image](https://user-images.githubusercontent.com/31904439/229591084-7b19aebe-6af9-4794-b7f8-bd44b81af7ee.png)





