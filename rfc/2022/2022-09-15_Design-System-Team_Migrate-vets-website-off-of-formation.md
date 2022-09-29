# RFC: formation and USWDS1 will be removed from `vets-website` and `content-build` and will instead use the Design System's new `css-library` based on USWDS3

* Comment Deadline: `2022-10-11`
* Team Crew and Name: Platform/DST
* Authors:
  * [Brooks Johnson](https://github.com/bkjohnson)
  * [Contributing Author 1 Github Handle](https://github.com/ContributingAuthor1Handle)
  * [Contributing Author 2 Github Handle](https://github.com/ContributingAuthor2Handle)
* [Original RFC Pull Request](https://github.com/department-of-veterans-affairs/va.gov-platform-arch/pull/-1)

## Background

`formation`, the CSS framework for va.gov, is dependent on USWDS version 1 as is the rest of va.gov. This is now a dead branch and the latest version of USWDS is version 3.
In order to continue to receive updates and bug fixes from USWDS, and also contribute back, we need to switch to a more recent version.

## Motivation

The Design System team is developing a new CSS library which is based on USWDS3 and may contain additional VA-specific styling.
By relying on USWDS internals, we are able to provide a lot of utilities while having to maintain less SASS ourselves.
We also want to namespace all of the classes we provide rather than allow developers full unlimited access to everything within USWDS.
This gives the Design System more control and will also make future maintenance and updates easier.

We also hope to be able to improve performance with this new css library in a number of ways, including:

- smaller stylesheets due to elimination of `!important` declaration
- smaller stylesheets due to simpler utility names (`vads-u-background-color--gray` => `vads-bg-gray`)

 [Early work](https://github.com/department-of-veterans-affairs/component-library/pull/452) on the css library revealed that for equivalent color utilities, removing the `!important` and shortening the class names resulted in a 40% reduction in file size.

(Note: these name changes are why teams will need to update their apps to use the names of the new classes)

We'd also like to explore the possibility of allowing teams to rely less on global site-wide assets and declare a minimum subset of which DS assets they will need. Much of this work is out of scope for this RFC.

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

We will be starting with migrating the utility classes, and the css library will export a `utilities.css` file as part of its build which can be imported in either location. All of the steps outlined here will begin with a single app, and then expanded to other apps once the pilot app has been successfully migrated.

#### Remove old VADS utility classes from markup
We will first identify a team and/or app and work with them on migrating their markup to use the new USWDS3-based utilities provided by `css-library`. As part of this we should also see if we can eliminate any unused CSS from the application's SASS file.

As we eliminate `vads-u-*` classes from app code, add an [ESLint override](https://eslint.org/docs/latest/user-guide/configuring/configuration-files#how-do-overrides-work) to their app directory to throw an error if the older style of utility class is added back into a file.

#### Remove component/module CSS

This is related to the prerequisite for migrating to web components. For example, [this bit of app code](https://github.com/department-of-veterans-affairs/vets-website/blob/4885e8c532f77801712a6d39c0625f8ceb19a556/src/applications/vaos/new-appointment/components/VAFacilityPage/FacilitiesNotShown.jsx#L81) relies on [this CSS from formation](https://github.com/department-of-veterans-affairs/veteran-facing-services-tools/blob/f0e1d666503ecf4aafcb421bbc47fc7f76abec4a/packages/formation/sass/modules/_m-additional-info.scss#L1-L4), and that module is [included in the sitewide styles for `vets-website`](https://github.com/department-of-veterans-affairs/vets-website/blob/4885e8c532f77801712a6d39c0625f8ceb19a556/src/platform/site-wide/sass/style.scss#L16). As we get to a point where we can be sure that certaion SASS modules are no longer being used, we can remove them from `vets-website`, giving us a good idea of how far along we are in the migration while also reducing the bundle size.

#### Try to replace "Custom CSS" with utility classes

Many applications have custom CSS [like this](https://github.com/department-of-veterans-affairs/vets-website/blob/fad1a404cc2c7a8c33153d159523c14888d6fa03/src/applications/gi/sass/partials/_gi-search-page.scss#L303-L310) where they are making declarations in a CSS file when there are existing CSS utility classes for the exact same declarations that could be used instead. We will also want to do an audit to identify places like this where utility classes can be used and then make changes to use the appropriate utilities.

This will have a performance benefit as well, since an audit of that particular CSS class would reveal that the `search-result-tag` CSS class isn't used in application code at all, so we are shipping unused CSS that should be deleted. As we begin to rely more on utility classes and developers write less CSS, this type of thing will happen less often.

#### Prune unneeded pieces of formation

This is somewhat covered in the "Remove component/module CSS" section, but as we make progress with the migration we will routinely run searches to see if there is any remaining code which relies on a given CSS class that is part of the migration. If there isn't, we can remove `formation` imports from `vets-website` which will let us get a better picture of which classes are actually in use. This will also improve performance by sending less CSS.

#### Identify styles/classes what have no component or utility available

As we audit and migrate each app we may find that there is custom styling happening which cannot be replaced using current components or utilities. When this happens we will make a note of the files & styles and consider if they should be brought into the DS in one way or another.

## Risks

### Breaking the appearance of a page or application

The goal is to reduce this risk as much as possible by making small incremental changes that are tested each step of the way. Relying on ESLint rules to ["stop the bleeding"](https://lethain.com/migrations/) should prevent new code from being added which uses older classes that have been replaced.

## Alternatives

### Moving directly to USWDS3 instead of the VADS CSS library

The problem with this is that it would leave us in the same situation wer're in now where future changes are more difficult.
By limiting the available CSS classes in `vets-website`, we will have an easier time making upgrades.

Part of the problem we're dealing with now is that `usa-*` classes are used throughout `vets-website`, and these are corresponding to USWDS1 styles. USWDS3 uses many of the same names like `usa-alert` which means that a drop-in replacement/upgrade is impossible to do with confidence that it won't break existing styles. Putting the VADS between `vets-website` and direct use of USWDS means that the DST will be able to manage these kinds of version upgrades in the future while minimizing risk in `vets-website`.

### Sticking with formation & USWDS 1

This will lead to further version skew and make it harder to adopt USWDS fixes and changes. This would also mean that more money is spent just to have the va.gov and the VADS put in work to "stay in sync with USWDS" while being stuck on USWDS1 and not actually receiving any direct benefits of the work of USWDS developers & designers.

## Diagrams

<!--
Try to visually represent the proposal using a [diagram](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-diagrams). [Live editor](https://mermaid.live/)

-->

