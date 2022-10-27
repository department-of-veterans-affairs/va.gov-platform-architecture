# RFC: Component Testing Best Practices for VFS Apps

* Comment Deadline: `2022-11-16`
* Team Crew and Name: VSP Platform
* Authors:
  * [Chris Alexander](https://github.com/alexandec)
* [Original RFC Pull Request](https://github.com/department-of-veterans-affairs/va.gov-platform-architecture/pull/50)

## Background

VA Platform teams have seen requests from VFS engineers for additional guidance on testing their React applications. Platform engineers have also observed that many VFS applications do not meet the highest standards in their application tests. In manay cases, teams are not taking advantage of powerful new tools which would help them write shorter but more thorough tests. These tools are already available in the platform, but teams are not aware or are reluctant to use them.

## Motivation

VFS application engineers need additional guidance to ensure all their application tests meet the highest standard. For example, many existing VFS app tests do not use React Testing Library or msw. Some applications do not use sinon sandboxes or otherwise properly clean up mocks/stubs. And code coverage is not always optimal.

Clearly documenting best practices for component testing will help VFS engineers improve their tests. Guiding VFS engineers with regard to the proper component testing tools and techniques will improve VA's return on investment, reducing bugs while encouraging refactoring.

## Design

The [(DRAFT) Component Testing Guidelines]([url](https://vfs.atlassian.net/wiki/spaces/~481106865/pages/2316763221/DRAFT+Component+Testing+Guidelines)) page contains proposed best practices, including justifications, and links. The page is ready for review.

## Risks

* VFS engineers may not read the Component Testing Guidelines page, and instead may reference existing poor practices embedded in certain VFS applications. Publicizing the new page should help address this issue.
* Engineers may forget to reset all mocks/spies and localStorage/sessionStorage, even though the guidelines call this out. We could address this by globally automating resets after every test.

## Alternatives

* Our existing [Unit Testing documentation]([url](https://vfs.atlassian.net/wiki/spaces/pilot/pages/1836187655/Unit+tests)) covers some basics, but contains limited recommendations on component testing. Further, existing documentation often does not specify the "why" behind its recommendations and sometimes lacks sources where engineers can learn more.
* Various best practices are described across the web, but these are scattered and it can be challenging to know which practices are optimal to adopt. Sifting through this would be time-consuming for engineers, with uneven results.
* Reading through unit tests for existing apps can provide examples of best practices. However, our codebase contains a mix of good and poor practices with no indication of the best path to follow.
