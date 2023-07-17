# RFC: The Platform shall provide a lightweight App path for VFS frontends

<!--
The title is what you want comments on. Use the active voice in a future tense.
Example:
    - The website will be built using the XZY framework
-->

* Comment Deadline: `2023-06-10`
* Team Crew and Name: <!--e.g. Platform/AMT-->
* Authors:
  * [@dcloud](https://github.com/dcloud)
  * [Contributing Author 1 Github Handle](https://github.com/ContributingAuthor1Handle)
  * [Contributing Author 2 Github Handle](https://github.com/ContributingAuthor2Handle)
* [Original RFC Pull Request](https://github.com/department-of-veterans-affairs/va.gov-platform-arch/pull/-1)

## Background

> Explain the current state. What is the problem? What needs to happen? Provide enough background for someone new to the problem space to understand this decision. Use active voice, present tense, and decisive language.
> Example: We need to choose how the website will render in the browser. There are several competing frameworks to choose from.

VA.gov frontend pages perform poorly for a significant number of Veterans. As of late 2022, 48.35% of users to VA.gov use slower mobile devices (Source: Google Analytics, 7/26/22-8/01/22). A significant amount are also on slow third-party devices, such as library desktops, per [government research](https://docs.fcc.gov/public/attachments/DOC-357270A1.pdf). Automated reports on the [Frontend support dashboard](https://department-of-veterans-affairs.github.io/veteran-facing-services-tools/frontend-support-dashboard/lighthouse-performance-report/) and in Datadog via [RUM's Core Web Vitals](https://docs.datadoghq.com/real_user_monitoring/browser/monitoring_page_performance/#core-web-vitals) show us that many pages on VA.gov perform poorly for Veterans. We need to optimize VA.gov frontend applications for better performance.

The VA.gov Platform provides [few options for creating new frontend (FE) applications and features](https://depo-platform-documentation.scrollhelp.site/developer-docs/adding-applications-and-features). As the Frontend Support Dashboard shows, most applications are loading multiple large JavaScript bundles that impact performance by increasing page load times, even when the application does not use the code those bundles provide. The VA.gov Platform needs to support options for smaller frontend applications.


## Motivation

>Which problem(s) does this solve? Why? Use active voice, present tense, and decisive language.
> Example: Framework XYZ is a modern, well-known, web rendering framework. It is easy to learn, easy to use, and provides high-performance web rendering. etc

### We can build smaller, faster and simpler applications using static site generation

Static-site generation techniques allow us to build smaller, faster applications by generating HTML and other assets at build time, minimizing HTML generation on user devices. We can add dynamic functionality selectively, and deliver smaller bundles of JavaScript. Server-side processing at build time and run time handle business logic and provide just enough data to our frontends only when they need it.

### Speed and simplicity of software development

Beyond performance for the end user, the option to build static or server-side rendered (SSR) HTML page opens up work to engineers not deeply experienced with React. SSR code can also be simpler to understand and maintain in comparison to complex React applications.

### Static content is weighed down with unneeded assets

Given that the VA.gov homepage, primarily uses static HTML content, consists of 4.88 MB of assets (HTML, CSS, JavaScript) (Accessed 24 April 2023) and includes three copies of React in different JavaScript bundles, there is plenty of opportunity for reducing overall page size. If we also [budget for performance](2023-05-01-va-rfc-frontend-performance.md), we will have useful goals for application size.


## Design

> Explain the proposed design in enough detail so that a team member will fully understand the implementation. Include a diagram (in the `images` dir) as needed to convey your plans. Use active voice, present tense, and decisive language.

The Platform will provide a minimalist option for application teams to create new applications that prioritize generating HTML at build time and do not require React. To start, this can be done with small adjustments to existing tools, e.g. webpack, to allow application manifests to specify non-JSX entry points, exclude React and related libraries, and allow for VFS teams to use/extend existing EJS templates.

VFS teams using the minimalist option will need to adopt a [performance budget](va-rfc-frontend-performance.md), and ensure that complex logic and data manipulation is performed server-side. Minimalist frontends should be able to fetch data that is immediately renderable to the end user. Since `vets-api` already is already responsible for managing and processing dynamic and personalized data, as well as feature toggles, the backend is already well-suited for generating ready-to-display data for frontends.

Representatives from VFS teams and the Platform should work together to explore what resources might aid a minimalist application; given that the design system is using web components, are there tools or guidance we might want to adopt for when and how to use web components?

VFS and Platform engineers should also look into what tools might help existing applications achieve better performance by shifting more logic to `vets-api` and HTML generation to build time. Frameworks like Next.js support [static HTML export](https://nextjs.org/docs/advanced-features/static-html-export), which might provide a migration path for existing applications.

## Risks


>List the risks of this approach
> * There are always risks. What are the risks of this solution?
> * These are the things people will bring up in opposition to your idea or plans. Acknowledge them.


- There aren't enough guide rails without a framework like React.
    - Platform and VFS teams should evaluate minimalist JS frameworks ( [alpinejs](https://alpinejs.dev), [htmx](https://htmx.org), etc.) for lightweight interactivity/dynamic content.
    - Platform and VFS teams can collaborate on best practices and update the Platform documentation to reflect those best practices. Those best practice could also be incorporated into issue templates and collab cycle reviews.
- SSG will require lots of additional tools, creating its own complexity
    - `vets-website` already has support for ejs templates, and app entry points don't intrinsically have to use React, so we already have the tools we need
    - Next.js provides SSG tools for React applications, and that's something that could be explored further
- We'll end up with "Not Invented Here" roll-your-own solutions
    - Platform docs, standardized templates, and the collab cycle should mitigate this
    - Various next-generation tools, e.g. Next.js, Svelte, Alpine.js, HTMX, could be evaluated and adopted

## Alternatives

> List the alternative approaches
> * There are always alternatives. What other alternative solutions were considered? Not considered?
> * What are their strengths, weaknesses, risks? Why weren’t they chosen?
> * Do not allow bias of a solution to show in this section, ensure each alternative has been considering seriously or do not list it in this section

### Incremental adoption of a new framework

Rather than adopt a lightweight apps path based on existing tools, we could adopt a new framework that provides static page generation options, like Next.js. This would need to be done incrementally, since adopting this across `vets-website` would be a major shift and involve work across all VA.gov teams. Taking this approach would require engineers to become familiar with the best practices and pitfalls of another framework. There may be a significant population of engineers with this knowledge who could help develop expertise across the Platform and VFS teams. It would still be important to measure and budget for performance. This approach probably requires further exploration and could not be immediately adopted. An article by Vercel, the company behind Next.js, [outlines some of the techniques and challenges migrating from React to Next.js](https://vercel.com/blog/migrating-a-large-open-source-react-application-to-next-js-and-vercel).

## References

<!--
This section /may/ be eliminated if it is not applicable.
When linking to other documents in this repository, ensure to link to their state at a particular commit (hence the usage of .../blob/<FULL_COMMIT_HASH>/...) as opposed to their current state (i.e. 'currently in main').
-->

- [VA Platform: Adding applications and features](https://depo-platform-documentation.scrollhelp.site/developer-docs/adding-applications-and-features)
- [Core Web Vitals as a UX measure](https://web.dev/vitals/)
- [Designing for Performance: Performance is User Experience](https://designingforperformance.com/performance-is-ux/)
