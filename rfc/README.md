# Requests For Comment

An RFC is a [Request for Comments](https://en.wikipedia.org/wiki/Request_for_Comments). It is a reflection of a team's thoughts and plans _at that moment_.

We write an RFC to _communicate intent and get feedback_ before we start building something. [RFC driven development](https://engineering-management.space/post/rfc-driven-development/) is a good concept to consider as we build for the Platform.

Whenever your team is making a decision that could affect the platform, write the details and get feedback from others on the Platform. Anyone - including product managers, UX, research, and engineers - is encouraged to create RFCs in order to solicit ideas in preparation of a decision direction.

There is no formal requirement to present an RFC at the [Platform Architecture Working Group meeting](https://vfs.atlassian.net/wiki/spaces/ECP/pages/1872363553/Platform+Architecture+Working+Group), but authors may _request_ to present there. Depending on the subject and/or scope of an RFC the Platform Architecture WG _may_ request author(s) to present either by its own authority or because it has been petitioned to do so.
Requesting an RFC's presentation is as simple as adding the RFC's PR to [the docket of the next scheduled meeting](https://vfs.atlassian.net/wiki/spaces/ECP/pages/2160132128/Working+Group+Meeting+Notes) along with a brief explanation of what the desired outcome of presentating is.

## How do I create an RFC and manage its lifecycle?

An RFC's lifecycle is managed through the GitHub Pull Request process and via these outlined, ordered steps:

1. Primary Author: create the RFC document and Pull Request.
    * Create a [new file](https://github.com/department-of-veterans-affairs/va.gov-platform-architecture/new/main/rfc/2022) in the repository.
    * Name the file in the format: `2022-01-01_<Team-Name>_<Id>_<Decision-Statement>.md`
      * `id` is optional, it can be used to indicate a unique ID to enable a team to precisely and easily refer to a specific RFC (e.g. "`TeamX_1248_Decision-Statement-Here`")
    * Copy [the RFC template](_template.md) content into the new file.
    * Click "Propose new file"
    * Title the Pull Request with this format: `<Team-Name>_<Id>_<Decision-Statement>`
    * Update the RFC document to link to the newly opened pull request
1. PR creation will automatically trigger:
    * Announcement in the `#platform-architecture` Slack channel.
    * Default assignees based on `CODEOWNERS`, these must remain in place.
    * Attachment of default labels (e.g. `RFC`), these must remain in place.
    * Addition of the PR to the [Platform Architecture](https://github.com/orgs/department-of-veterans-affairs/projects/710/views/1) board in the "Draft" column.
1. Contributing Authors:
    * Collaborate on the RFC document in its dedicated branch until it is in a state where it can receive comments.
1. Primary Author:
    * When collaborators have finished updating the RFC and it is ready for public comment, move the RFC from the "Draft" column to "Ready" on the [Platform Architecture project board](https://github.com/orgs/department-of-veterans-affairs/projects/710/views/1).
    * Update the Comment Deadline in the RFC document to an appropriate deadline. 30 days from when the RFC is moved into the "Ready" state is recommended.
1. Collect comments via the Pull Request (see also [How do I comment on an RFC](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/commenting-on-a-pull-request)) until the Comment Deadline.
1. Address and resolve every comment, all comments must be resolved before the Pull Request can be merged into `main`. Every comment must receive a response by the author(s) in accordance with [addressing comments](#addressing-comments).
1. Once all comments are resolved and the comment deadline is reached, the `CODEOWNERS` will approve the PR and it will become mergeable. The Primary Author merges the RFC into the `main` branch, thereby completing the RFC.

Upon finalization of the RFC, if further discussion should be needed, then a new RFC must be created to facilitate this discussion.

## Guidance on Sensitive Information

> This repository is public, do not include information considered sensitive in this repository.

Be mindful that **RFCs are public**. The content in this repository follows [the same guidance and policy that applies to the va.gov-team repository](https://github.com/department-of-veterans-affairs/va.gov-team/blob/master/platform/working-with-vsp/policies-work-norms/sensitive-guidance.md).
Authors of RFCs **must not** include sensitive information in RFCs. In almost all cases, it will be possible to write the RFC without including sensitive information (there are _parallels_ to be drawn for what would be sensitive or not using "encryption algorithm vs encryption key": it is _fine_ for the _algorithm_ to be public, they _key_ must never be public).

If it is not possible to exclude the sensitive information from the RFC, first try again, and if this remains the case, use the sensitive repository instead of this repository. There will always be an opportunity to move it from the sensitive repo back into a public repo, but never the other way around.
All that being said: the utmost effort should be applied to adhere to our "Default to Open" stance, and specifically: try to author the RFC in such a way that it does not contain any information that would be considered sensitive, thus enabling it to live in this repository.

Be mindful that writing an RFC may involve multiple commits: it is not only the final state of the RFC that is available, but every commit (until merged); in other words, your mindfulness around sensitive information should extend over the entirety of the authoring of the RFC.

Similarly, RFCs will inherently attract comments; do not include sensitive information in comments added to the RFC. If comments involve sensitive information, provide those via other mechanisms and come a conclusion with the author(s) of the RFC on how to incorporate the comment(s) while excluding the sensitive part of the comment.


## Roles and Responsibilities

This section outlines the different roles and their responsibilities in this process.

### Primary Author

The Primary Author is responsible and accountable for the majority of the activities that make up the RFC process, including the overall shepherding of the RFC through the process. Any activity not explicitly delegated to another actor is assumed to be the responsibility of the Primary Author. Some explicitly called out responsibilities are:

* Bringing the RFC into existence via a new branch, containing the RFC document populated with all necessary information.
* Ensuring that the content of the RFC document meets quality standards and represents a meaningful RFC.
* Creation of the PR _once the RFC document is ready to receive comments_.
* Soliciting participation in the RFC via announcement in relevant channels and discussions.
* Ensuring all comments are appropriately addressed and when appropriately addressed, that they are subsequently resolved in the PR.
* Enforcing adherence to the stipulated timeline(s) for the RFC, including enforcement of the Commenting Period.
* On conclusion of the RFC, after approval[1] from at least one of the registered `CODEOWNERS` of the RFC subsequently merging the PR into the `main` branch.

The Primary Author has the prerogative to ignore, discard, or include comments that were registered outside the stated Commenting Period, into the record.

### Contributing Author

Not all RFC will have Contributing Authors; when there are Contributing Authors, their responsibilities will typically entail:

* Collaborate with the Primary Author on the content of the RFC within the provided timeline.
* _Address_ registered comments on their sections but **not** resolve them.

While Contributing Authors _could_ also contribute as Commenter, this is generally discouraged and any comments they may have should be incorporated in the original document. However, we want to enable commenting by Contributing Authors on sections to which they did not contribute if there are dissenting opinions between authors that could not be resolved during the creation of the document and would benefit from public, civil discourse.

### Commenter

Commenters provide comments on the document as it stands within the PR, their responsibilities include:

* Provide constructive, meaningful, and substantive comments to the RFC's content.
* Ensure comments are linked to the _entire_ section to which they apply. It is advised that comments provide a suggested alternative to the section on which they comment.
* Ensure that the comment is resolved in a meaningful fashion, which _may_ be a declining of incorporation of the comment by the author(s).
    * When there is serious dissent with how a comment was addressed, ensure that this dissent is registered and gets addressed.

## Addressing Comments

Every registered comment _must_ be addressed by the Primary Author or the author of the section to which the comment applies. A comment can be addressed by:

* Incorporation: the comment is incorporated into the document by a subsequent commit which becomes part of the PR.
* Resolution through discussion without a need for incorporation: a resolution to the comment is reached via threaded discussion and no changes to the document are required.
* Acknowledgment and declining: the comment is acknowledged, a rationale is provided for why the comment will not be incorporated into the document.

Comments **shall not** be resolved without being addressed.

## Relation to ADRs

An RFC may 'graduate', potentially in combination with other RFCs, into an ADR. This process is described in the ADR Process.

## Footnotes

* [1]: Note that this approval is _not an approval of the content_ of the RFC, but instead _reflects approval that the process was followed_ as outlined in this document.
