FC: _decision statement_

## Upgrade React Router from version 3 to version 5

    * Comment Deadline: `2022-06-17`
    * Team Crew and Name: Platform Spike / Forms Library 
    * Authors:
      * [micahchiang](https://github.com/micahchiang)
      * [bjmccotter7192](https://github.com/bjmccotter7192)

## Related Issues
- https://github.com/department-of-veterans-affairs/va.gov-team/issues/34572
- https://github.com/department-of-veterans-affairs/va.gov-team/issues/35469
- https://github.com/department-of-veterans-affairs/va.gov-team/issues/36088
- https://vfs.atlassian.net/wiki/spaces/FLT/pages/2196701189/Vets-Website+React+Router+Upgrade

## Background
`vets-website` currently runs an on outdated version of react-router, version 3. Upgrading react-router to v5 will unlock new routing features for app teams to use, as well as provide a clear step towards upgrading to react-router v6, a step [recommended in the react-router docs](https://reactrouter.com/docs/en/v6/upgrading/v5#upgrade-to-react-router-v51), and a dependency used by [VA Forms System Core](https://github.com/department-of-veterans-affairs/va-forms-system-core). 

## Motivation
Motivation for this upgrade is to keep `vets-website` dependencies up to date, unlock new react-router features for application teams, and bring the repository to a state where VA Forms System Core (VAFSC) can be used effectively.

## Design
Upgrade react-router from version 3 to 5 in all platform and application code. From a high level here's what's needed:
- Route definitions will be updated to use router components instead of regular javascript objects
- Route redirects will be updated to use react-router `Redirect` components
- Current forms library code will need to be updated to use new router components
- `react-router` imports will need to be updated

## Risks
This effort for this upgrade is significant. `react-router` is a core dependency in vets-website and is used extensively throughout the repository. In addition to it being used in `platform/` code and several non-form applications, `react-router` is an integral part of generating routes for all forms on VA.gov. 

With this, a change to how the forms library generates routes automatically propagates to all forms in production. If possible, there needs to be a way to roll this upgrade out piecemeal to different forms, and a plan in place to rollback if forms break in production as a result of the upgrade. 

## Alternatives
1. Continue using react-router-v3 - Though this is technically an alternative, it isn't viable. As more and more teams onboard to the platform, familiarity with version 3 of `react-router` will continue to lessen. There shouldn't be an expectation for teams to learn an outdated version of an industry standard library. 
