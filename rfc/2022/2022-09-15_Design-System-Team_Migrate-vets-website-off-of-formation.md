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

Before this migration can begin there are prerequisites which must happen.

### Prerequisites

1. Application, sitewide, & platform code will need to be using proper VADS web components. This means no React components,
and no CSS class components as these rely on `formation` components.
    - This will unblock those modules from being removed in formation
1. We will need a published version of the `css-library` package
1. Write a new ESLint rule which can trigger when a `vads-u-*` class is used.

Once these have been met we can move on to beginning the migration

### Migration

In order to begin migrating we will need to make the `css-library` available, and this can be done in one of two ways.

1. Add the stylesheets to the sitewide SASS file.
    - This will cause every page to download the file whether they use the utilities or not
    - Could be a "final state" of the migration
1. Add the stylesheets to each specific app's SASS file as they are migrated.
    - This will bloat the app's bundle size, but other pages and apps won't be affected


#### Remove old VADS utility classes from markup
We will first identify a team and/or app and work with them on migrating their markup to use the new USWDS3-based utilities provided by `css-library`. As part of this we should also see if we can eliminate any unused CSS from the application's SASS file.

As we eliminate `vads-u-*` classes from app code, add an [ESLint override](https://eslint.org/docs/latest/user-guide/configuring/configuration-files#how-do-overrides-work) to their app directory to throw an error if the older style of utility class is added back into a file.

#### Remove component/module CSS

This is related to the prerequisite for migrating to web components. For example, [this bit of app code](https://github.com/department-of-veterans-affairs/vets-website/blob/4885e8c532f77801712a6d39c0625f8ceb19a556/src/applications/vaos/new-appointment/components/VAFacilityPage/FacilitiesNotShown.jsx#L81) relies on [this CSS from formation](https://github.com/department-of-veterans-affairs/veteran-facing-services-tools/blob/f0e1d666503ecf4aafcb421bbc47fc7f76abec4a/packages/formation/sass/modules/_m-additional-info.scss#L1-L4), and that module is [included in the sitewide styles for `vets-website`](https://github.com/department-of-veterans-affairs/vets-website/blob/4885e8c532f77801712a6d39c0625f8ceb19a556/src/platform/site-wide/sass/style.scss#L16). As we get to a point where we can be sure that certaion SASS modules are no longer being used, we can remove them from `vets-website`, giving us a good idea of how far along we are in the migration while also reducing the bundle size.

#### Prune unneeded pieces of formation

This is somewhat covered in the previous section, but as we make progress with the migration we will routinely run searches to see if there is any remaining code which relies on a given CSS class that is part of the migration. If there isn't, we can remove `formation` imports from `vets-website` which will let us get a better picture of which classes are actually in use. This will also improve performance by sending less CSS.

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

