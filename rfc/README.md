# Requests For Comment

An RFC is a [Request for Comments](https://en.wikipedia.org/wiki/Request_for_Comments). It is a reflection of a team's thoughts and plans _at that moment_.

We write an RFC to _communicate intent and get feedback_ before we start building something. [RFC driven development](https://engineering-management.space/post/rfc-driven-development/) is a good concept to consider as we build for the Platform.

Whenever your team is making a decision that could affect the platform, write the details and get feedback from others on the Platform. Anyone - including product managers, UX, research, and engineers - is encouraged to create RFCs in order to solicit ideas in preparation of a decision direction.

## How do I create an RFC and manage its lifecycle?

An RFC's lifecycle is managed through the GitHub Pull Request process and via these outlined, ordered steps:

1. Author the RFC document.
    * The RFC's Primary Author creates a new branch in [this repository](https://github.com/department-of-veterans-affairs/va.gov-platform-architecture), sourced from the `main` branch.
    * The Primary Author starts the RFC document in MarkDown by copying [the RFC template](_template.md) to a new file in the `/rfc/YYYY/` directory where `YYYY` is the year of the RFC creation.
        * e.g. "`/rfc/1970/1970-01-01 - <team_name> - <id> - <decision_statement>.md`"
    * All necessary authors collaborate on the RFC document in the dedicated branch until it is in a state where it can receive comments.
1. The Primary Author opens a new Pull Request from the dedicated branch into `main`.
    * The title of the Pull Request _must contain the comment deadline_ and must reflect the decision statement from the RFC (e.g. "`RFC - YYYY-MM-DD - <decision_statement>`").
    * Update the RFC document with a link to the PR representing this process.
1. PR creation will automatically trigger:
    * Announcement in the `#platform-architecture` Slack channel.
    * Default assignees based on `CODEOWNERS`, these must remain in place.
    * Attach default labels (e.g. `RFC`), these must remain in place.
    * Addition of the PR to the [Platform Architecture](https://github.com/orgs/department-of-veterans-affairs/projects/710/views/1) board in the "Draft" column.
1. Collect comments via the Pull Request (see also [How do I comment on an RFC](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/commenting-on-a-pull-request)) until the Comment Deadline.
1. Address and resolve every comment, all comments must be resolved before the Pull Request can be merged into `main`. Every comment must receive a response by the author(s) in accordance with [addressing comments](#addressing-comments).
1. Once all comments are resolved and the comment deadline is reached, the `CODEOWNER` will approve the PR and it will become mergeable. The Primary Author merges the RFC into the `main` branch, thereby completing the RFC.

Upon finalization of the RFC, if further discussion should be needed, then a new RFC must be created to facilitate this discussion.

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
