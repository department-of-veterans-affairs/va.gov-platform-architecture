# RFC: formation and USWDS1 will be removed from `vets-website` and `content-build` and will instead use the Design System's new `css-library` based on USWDS3

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

### Breaking the appearance of a page or application

The goal is to reduce this risk as much as possible by making small incremental changes that are tested each step of the way. Relying on ESLint rules to ["stop the bleeding"](https://lethain.com/migrations/) should prevent new code from being added which uses older classes that have been replaced.

## Alternatives

### Moving directly to USWDS3 instead of the VADS CSS library

The problem with this is that it would leave us in the same situation wer're in now where future changes are more difficult.
By limiting the available CSS classes in `vets-website`, we will have an easier time making upgrades.

### Sticking with formation & USWDS 1

This will lead to further version skew and make it harder to adopt USWDS fixes and changes.

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
