# RFC: formation and USWDS1 will be removed from `vets-website` and `content-build` and will instead use the Design System's new `css-library` based on USWDS3

<!--
The title is what you want comments on. Use the active voice in a future tense.
Example:
    - The website will be built using the XZY framework
-->

* Comment Deadline: `YYYY-MM-DD`
* Team Crew and Name: Platform/DST
* Authors:
  * [Brooks Johnson](https://github.com/bkjohnson)
  * [Contributing Author 1 Github Handle](https://github.com/ContributingAuthor1Handle)
  * [Contributing Author 2 Github Handle](https://github.com/ContributingAuthor2Handle)
* [Original RFC Pull Request](https://github.com/department-of-veterans-affairs/va.gov-platform-arch/pull/-1)

## Background

`formation`, the CSS framework for va.gov, is dependent on USWDS version 1 as is the rest of va.gov. This is now a dead branch and the latest version of USWDS is version 3.
In order to continue to receive updates from USWDS, we need to switch to a more recent version.

## Motivation

The Design System team is developing a new CSS library which is based on USWDS3 and may contain additional VA-specific styling.
By relying on USWDS internals, we are able to provide a lot of utilities while having to maintain less SASS ourselves.
We also want to namespace all of the classes we provide rather than allow developers full unlimited access to everything within USWDS.
This gives the Design System more control and will also make future maintenance and updates easier.

## Design

<!--
Explain the proposed design in enough detail so that a team member will fully understand the implementation. Include a diagram (in the `images` dir) as needed to convey your plans. Use active voice, present tense, and decisive language.
-->
## Risks

<!--
List the risks of this approach

* There are always risks. What are the risks of this solution?
* These are the things people will bring up in opposition to your idea or plans. Acknowledge them.
-->

## Alternatives

<!--
List the alternative approaches

* There are always alternatives. What other alternative solutions were considered? Not considered?
* What are their strengths, weaknesses, risks? Why weren’t they chosen?
* Do not allow bias of a solution to show in this section, ensure each alternative has been considering seriously or do not list it in this section
-->

## Diagrams

<!--
Try to visually represent the proposal using a [diagram](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-diagrams). [Live editor](https://mermaid.live/)

-->

## References

<!--
This section /may/ be eliminated if it is not applicable.
When linking to other documents in this repository, ensure to link to their state at a particular commit (hence the usage of .../blob/<FULL_COMMIT_HASH>/...) as opposed to their current state (i.e. 'currently in main').
-->

* [RFC XYZ](https://github.com/department-of-veterans-affairs/va.gov-platform-arch/blob/<FULL_COMMIT_HASH>/rfc/<FILENAME>.md): <RFC_TITLE>
