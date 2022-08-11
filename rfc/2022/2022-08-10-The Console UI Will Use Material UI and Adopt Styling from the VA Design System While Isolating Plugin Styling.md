# RFC: _The Console UI Will Use Material UI and Adopt Styling from the VA Design System While Isolating Plugin Styling_


* Comment Deadline: `2022-08-26`
* Team Crew and Name: <!--Console UI-->
* Authors:
  * [jchapin](https://github.com/jchapin)
  * [murphytwin](https://github.com/murphytwin)

## Background

The Platform Console is built upon [Backstage](https://backstage.io/) which includes components based on [material-ui](https://v4.mui.com/). Since the inception of the Platform Console, the Console UI team has been using the built-in design system of Material UI and minimally integrating the VA Design System styling into the product. However, after the first implementation of a plugin within the Console UI occurred, the Release Tools Team plugin noticed issues related to styling due to the choices of the plugin developers. The RTT plugin implemented styles that leaked outside of the plugin's bounds, affecting the entire system's appearance. After RTT and Console UI teams resolved this initial issue by scoping/namespacing styles to a specific CSS class, we saw the problem reoccur when the RTT team adopted more of the CSS defined by the VA Design System's "Formation" framework.

The [VA Design System](https://design.va.gov/about/) provides guidelines and codes to ensure consistency in digital services across the [VA.gov](https://www.va.gov/) platform. However, its primary audiences include those building veteran-facing services and applications on [VA.gov](https://www.va.gov/), whereas the Platform Console’s primary intended audience are VFS and Platform backend engineers. More importantly, the VA Design System would need to be implemented as custom components, [which would require substantial effort to rewrite and maintain code and risks incurring technical debt](https://vfs.atlassian.net/wiki/spaces/CUI/pages/2256306184/Draft+Explore+replacing+components+in+Console+UI+with+Custom+Components). 

## Motivation

We need to accomplish the following goals:

    1. Identify and adopt a design system strategy that will adhere to core design principles of the VA Design System while making it easy for the Platform Console to apply updates from the open-source project upon which the software is based.

    2. Document a strategy that allows plugin developers to adopt, extend, or opt-out of the design system Console UI is using within the confines of their plugin.

    3. Define a process for improving the accessibility of the product's chosen design system to meet or exceed WCAG 2.0 AA standards.

## Design

We will adopt the Material UI + VA Design System Styling utilized by the Lighthouse team, what they have dubbed the **VA Enterprise Design System**. This system will comply with all of the brand identity and accessibility standards of the VA Design System. The significant difference is that the VA Enterprise Design system has a different end-user base internal to the organization and focuses on software engineering. 

We will document how to adopt this system for plugin developers that need to extend the Console UI and don't need additional UI/UX flexibility. In addition, we will document how to extend the system for plugin developers who need to offer additional capabilities for visual content presentation within Console UI. We will also document how to disable all of the VA Enterprise Design System for plugin developers that need highly customized UI/UX or the need to demonstrate another design system, such as The VA Design System.

This will 1) allow the Console documentation and use to begin faster than anticipated, and 2) contribute to the formation of an internal “enterprise” design system so that non-veteran-facing tools can also remain consistent.

## Implementation strategy of VA Design System standards on the Platform Console

We can adopt the standards of the VA Design System while not adopting the system. The Material UI product that we inherit from upstream will have markup that is written to slightly different standards. Therefore, we will aim to achieve the same effect of components from the VA Design System by using Material UI components; this was be determined by an [audit comparing components between the two systems](https://vfs.atlassian.net/wiki/spaces/CUI/pages/2276196618/VADS+Backstage+Material+UI+Comparison+Summary) to assess similarities and differences. 

In addition, we will aim to adhere to the standards set forth in the [21st Century Integrated Digital Experience Act](https://www.congress.gov/bill/115th-congress/house-bill/5759/text). This act applies to public sites first and foremost, however internal tools are encouraged to perform to the same standards. The standards are as follows: 


## SEC. 3. WEBSITE MODERNIZATION.
    (a) Requirements for New Websites and Digital Services.--Not 
    later <>  than 180 days after the date of enactment of 
    this Act, an executive agency that creates a website or digital service 
    that is intended for use by the public, or conducts a redesign of an 
    existing legacy website or digital service that is intended for use by 
    the public, shall ensure to the greatest extent practicable that any new 
    or redesigned website, web-based form, web-based application, or digital 
    service--
    
            (1) is accessible to individuals with disabilities in 
        accordance with section 508 of the Rehabilitation Act of 1973 
        (29 U.S.C. 794d);
            (2) has a consistent appearance;
            (3) does not overlap with or duplicate any legacy websites 
        and, if applicable, ensure that legacy websites are regularly 
        reviewed, eliminated, and consolidated;
            (4) contains a search function that allows users to easily 
        search content intended for public use;
            (5) is provided through an industry standard secure 
        connection;
            (6) is designed around user needs with data-driven analysis 
        influencing management and development decisions, using 
        qualitative and quantitative data to determine user goals, 
        needs, and behaviors, and continually test the website, web-
        based form, web-based application, or digital service to ensure 
        that user needs are addressed;
            (7) provides users of the new or redesigned website, web-
        based form, web-based application, or digital service with the
        option for a more customized digital experience that allows 
        users to complete digital transactions in an efficient and 
        accurate manner; and
            (8) is fully functional and usable on common mobile devices.
            
## 3.a.1
We have tools scanning our Console UI environments reporting any accessibility issues. We have a specification written to report all of these issues to our project: [#562](https://github.com/department-of-veterans-affairs/platform-console-ui/issues/562)

We intend to conform to WCAG (Web Content Accessibility Guidelines) 2.x standards: [Web Content Accessibility Guidelines (WCAG)  2.0](https://www.w3.org/TR/WCAG20/) 

Additionally we are using Google Lighthouse to take a broad measurement of our accessibility. For our light theme we're already scoring 100% for their tests. We have contrast issues with some of our links that take us down to 98% when using a dark theme.

These are preventive steps; however, we would need to conduct user research with people using assistive technologies to better understand the accessibility of the Console.

## 3.a.2
To ensure consistent appearance within the Console and when compared to other VA products, we suggest the following strategy to synchronize design assets for Material UI with the styling changes from Formation, the front-end framework used on [VA.gov](https://www.va.gov/). 

Process for ongoing alignment of VADS, Material UI 
Strategy for development of lifecycle of recommended sharing
We suggest setting up a new repository for styling modifications to Backstage outside of either the Lighthouse instance or our own, so we can share and incorporate the styling into both products.

In that repository, we will likely need to set up automation that downloads the [source code for Formation](https://github.com/department-of-veterans-affairs/veteran-facing-services-tools/tree/master/packages/formation). This automation would consult [assets like images and fonts, SASS files](https://github.com/department-of-veterans-affairs/veteran-facing-services-tools/blob/master/packages/formation/sass/base/b-variables.scss) that set variables and synchronize the definitions to the design system used in Backstage. This automation could be based on the releases of [Formation](https://github.com/department-of-veterans-affairs/veteran-facing-services-tools/releases?q=Formation&expanded=true).

Both the Lighthouse and the Console UI projects will then need to incorporate the changes automatically compiled, and likely manually reviewed and tweaked to this new repository.

## Component alignment
We completed an [audit of components used in the VA Design System and compared the components of both systems](https://vfs.atlassian.net/wiki/spaces/CUI/pages/2276196618/VADS+Backstage+Material+UI+Comparison+Summary). This audit revealed that the large majority of components have comparable functionality between the two systems (Material UI and VA Design System). 

In addition, [we explored the technical challenges associated with how to import and use individual components from VA Design System in the Console](https://vfs.atlassian.net/wiki/spaces/CUI/pages/2256306184/Draft+Explore+replacing+components+in+Console+UI+with+Custom+Components). We concluded that overhauling the entire Console to use a set of custom components from the VA Design system presents a lot of issues. The work to implement and maintain is quite high and will likely result in more error-prone code (Backstage provided components from Material UI already handle many API interactions for us). Drifting further from Backstage/Material UI and their adopters community comes with costs as well, making updates more challenging, and not being able to benefit from community improvements to the integrated components.

Based on these activities, we suggest using the Material UI components that overlap with VA Design system components to avoid downstream technical debt. 

## 3.a.3
N/A - We overlap in terms of the software used with Lighthouse, but not in the audience and functionality. Otherwise we're a new product for the VA Platform.

## 3.a.4
A Search function can be found in the Catalog and we do have the ability to expose that search wherever needed.

![image](https://user-images.githubusercontent.com/100814257/184251359-93c96c82-2b74-4a45-a807-fafffc109cc1.png)

## 3.a.5
Our team is able to verify this, but this is the duty of the infrastructure team.

## 3.a.6
N/A for this RFC, but our engineering team can speak to the continual testing being done. Research and product can probably better speak to the data collected to influence management decisions.

## 3.a.7
Our work tying the identity of the user to their owned assets on GitHub, as well as the work to customize User and Team profiles satisfies this requirement: [#536](https://github.com/department-of-veterans-affairs/platform-console-ui/pull/536)

## 3.a.8
Most of our end-users are not on mobile. We have not been focusing on it. The design system in Backstage is responsive, but mobile users are clearly secondary in concern.

## Strategy for contributing upstream to VA Design System
In order to contribute upstream, we must:
    1. Publish release notes and proliferate them up to PO's and the teams they lead so they can see our work. 
    2. Understand the process for contributing to a product if we find deficiencies. 

## Risks

- Adopting another design system increases the development burden on plugin developers, who have to learn another design system.

- Plugin developers may prefer to use the VA Design System on VA.gov due to their familiarity with the system and its accessibility advantages.

- Giving plugin developers the option to adopt, extend, or opt-out may lead to confusion that leads to many opting out or breaking convention and routine, breaking the Console UI's styling as a worst-case.

## Alternatives

- Adopt the VA Design System’s (VADS) CSS and components used on VA.gov instead of the VA Enterprise Design System. It will make keeping updates from Backstage more complicated, and it may require extending the VA Design System with components for end-users who are engineers and not veterans.

    - An [assessment of the pros and cons to this approach](https://vfs.atlassian.net/wiki/spaces/CUI/pages/2256306184/Draft+Explore+replacing+components+in+Console+UI+with+Custom+Components) indicates replacing components in the Platform Console with VA Design System components would be technically onerous, require ongoing maintenance, and risks driving a drift away from Backstage’s code base. 

- Another alternative would be to adopt the [United States Web Design System (USWDS)](https://designsystem.digital.gov/). It would ensure the accessibility and visual identity of the Console UI would align with other federal IT sites.

    - This approach has similar obstacles as implementing the VA Design System.  

- Instead of allowing teams to extend the design system, don’t provide any strategy or documentation for extending Console UI’s system. They either have to use our system or provide their own while keeping the two systems isolated.

- Instead of allowing teams to opt-out, don’t allow plugin developers to commit any CSS. This choice would force them to use whatever design system the Console UI uses.


