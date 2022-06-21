# RFC: Engineering Decisions will be simplified to only include RFCs and ADRs

* Comment Deadline: `2022-06-24`
* Team Crew and Name: Platform/Leadership
* Authors:
  * [Jonathan Julian](https://github.com/jjulian)

## Background

Platform engineering decisions are often made outside of any formal process. Teams choose approaches to solve problems, maybe test some hypothesis', possibly get other engineering leads to understand and agree, and maybe get their product owner to approve the decision. 
There is an existing Engineering Decision process owned by the Platform Engineering Community of Practice, but in reality, the decision process is not often followed. The process has not been well-socialized over time and across leadership changes. 

## Motivation

**The current process is relatively unused.** In the last 2 years, the Platform has collected:

* 10 idea docs in confluence
* 9 design docs in github
* 25 RFCs in github
* 17 ADRs in confluence

This is about 1 ADR every two months, and 1 RFC per month. With two sprints per month, and more than 10 platform teams working, an argument could be made that teams should be producing ~5 RFCs per month (in aggregate), with at least half of them being promoted to ADRs.

**Platform teams will benefit** from a slightly more visible process:

* challenges and decisions other teams are working on will be visible to the entire platform
* challenges a team is working on will be more easily solved correctly by having more technical experts seeing the decision and alternatives
* teams will more fully understand their problem at hand by writing (prose, not code!) about their challenge and their suggested solution

**Platform crew leaders will benefit** from seeing more of the work being done in the open, and be able to steer decisions as needed without being directly involved. This will lead to higher-quality work on the Platform, and more efficient delivery of solutions, as the design stage will (in many cases) move faster.

## Design

* **Simplify the process**. The current process involves four artifacts that team members are required to understand and choose between: ADRs, Idea Docs, Design Docs, and RFCs. Practically, ADRs are the only one of these that are commonly requested. 
Idea docs and design docs are removed from the process for simplicity. Teams of course may still generate idea docs and design docs. But if the work they are doing affects multiple teams, has architectural impacts, or there are alternatives that the Platform should consider, then an RFC should be created in addition to these docs.

* **Author and track RFCs as github PRs**. RFC stands for Request for Comments, and the medium where an RFC is published should be optimized for discussion. PRs provide a framework for collecting suggestions, questions, and thoughts about code, until that code is in a state where everyoine involved agrees that it is ready for production. This same framework should be used for RFCs: the work plan (RFC text) is published, comments/suggestions are collected, answers to questions are incorporated into the text, then the RFC is “approved” via merge into the repo. Interested parties can easily “subscribe” to the RFC PR to get updates sent to their inbox.

* **Create a friendly and descriptive RFC template** in github, that contains hints to get authors to quickly create RFCs with little friction (one already exists; update as needed). The goal is to enable an RFC author to get their idea into a sharable state in less than 60 minutes. Create a PR description template `.github/PULL_REQUEST_TEMPLATE/rfc.md` to ensure a standard way of describing the RFC outside of the RFC file body.

* **Create a lightweight RFC PR process**. This could include setting a timebox around RFC lifetime, and a loose “approval” process that empowers the author to move forward once all comments have been resolved.

* **Move RFCs to a more visible location in the repo**. They currently live in the folder `/platform/engineering/request-for-comment/`, move them closer to the ADRs in dir `/docs`.


## Risks

* Simplifying the process will not lead to the desired outcome of more work being done in public

* The omission of design docs and idea docs will lead to less documentation

* Using a PR process may introduce a barrier to commenting for those not familiar with Github's pull request system and process.

## Alternatives

* The existing process could be socialized and enforced by platform leadership, requiring no changes.
  * this seems like an obvious solution, but it has not happened so far over the course of the Platform’s life
  * enforcement would need to fall onto some governing body; that body does not exist today
  * the existing process is too complex for the simple goal of doing more work in the open, as stated above

* RFCs could be authored and tracked in Confluence. Confluence allows documents to be easily commented on, and interested parties can easily “watch” an RFC to get updates send to their inbox. The main drawback with this approach is visibility - although all platform team members have access to confluence, VFS team members have a read-only view, limiting their participation. We want to encourage VFS team participation in the RFC process.

* RFCs could be tracked by a variety of other means in github:

  * **Github issues**: an RFC template would be used to create a new RFC issue, and comments/revisions would be made directly on that issue.

    * Pros: familiarity for engineers and product managers
    * Cons: lack of inline commenting; when an RFC is finalized, it becomes a closed issue, which is hard to discover later; github ui is less familiar to non-engineers

  * **Github Discussions**: Gatsby implements RFCs as Github Discussions.

    * Pros: may be the right tool for the job
    * Cons: this is new and the platform may not want to introduce a new tool

