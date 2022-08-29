# Hostname Transition Release Plan
**Cloud Isolation Team**
  - Andrea Singh (Engineering)
  - Alan Werner (Product)
  - Patrick Vinograd (Contributor)

Who is this document for?
  - Platform and VFS development teams that work on the front-end of vets-website or the Mobile Flagship app.
  - Platform and VFS development teams that work on vets-api.
  - Lighthouse development teams that are involved in the migration of the Lighthouse API gateway. 

## **Background**
Vets-api and Lighthouse currently share the same API endpoint: api.va.gov. In the near future Lighthouse will assume exclusive control of api.va.gov and consumers of VA.gov APIs will need to transition to using a new API hostname: platform-api.va.gov. The goal of the Cloud Isolation Team is to enable this separation of concerns.

## **Motivation**

The product goals and technical infrastructures of Lighthouse and VA.gov have diverged over time. Sharing the api.va.gov hostname is no longer feasible. In particular, Lighthouse wants to re-architect to use an Apigee API gateway hosted in Google Cloud (GCP). There's no reason for all VA.gov traffic to route through this gateway or through GCP. Because Lighthouse has a large number of API consumers, most of whom are external to VA, they have priority for continuing to use api.va.gov. VA.gov will transition to a new API hostname.


### New hostname for vets-api
Teams that use VA.gov APIs as an endpoint will need to update their application code to replace any hostname instances of `api.va.gov` with `platform-api.va.gov`.

Teams that use Lighthouse APIs as an endpoint will not be required to change their hostname addresses.


### Rerouting upstream requests from vets-api
The vets-api code base makes several API requests to different hosts. In instances where the hostname is configured to be `api.va.gov`, the reference will need to change. Depending on the use case, the change will be:
  - For upstream Lighthouse requests, the requests to *api.va.gov will be routed via specially configured ports in the Forward Proxy:

     - Port 4475 routes to the respective *api.va.gov host in all environments.
     - Port 4492 should be used in Dev and Staging to target the Lighthouse sandbox environment.

       Reference: [Forward Proxy Addresses](https://vfs.atlassian.net/wiki/spaces/CLOUD/pages/2263449831/Guide+API+Hostname+Status).
  - For requests that vets-api makes to itself, the endpoints need to change to the new, environment-specific *platform-api.va.gov hostname.
  - Host references that don’t make network requests should remain unchanged.
  - In one case, vets-api makes calls to Lighthouse servers directly. For this purpose we set up an additional fwdproxy port (`4494`) that points from the DSVA staging environment to Lighthouse's sandbox server at `blue.lab.lighthouse.va.gov`


## **Design**

### Completed Work  
#### **New hostnames**
  - Platform-api hostnames have been registered in the VA registry:
    - **Dev:** dev-platform-api.va.gov
    - **Staging:** staging-platform-api.va.gov
    - **Sandbox:** sandbox-platform-api.va.gov
    - **Prod:** platform-api.va.gov
  - ESECC has been approved for all hostnames.
  - DNS and SSL have been configured for all platform-api hostnames.
  - Platform-api hostnames are resolving to vets-api in all environments.

    Reference: [Hostname Specifications](https://vfs.atlassian.net/wiki/spaces/CLOUD/pages/2283339976).


#### **Vets-api: Settings**
The vets-api settings in the devops repository which reference `api.va.gov` have been identified and their change requirements have been documented.

Reference: [Vets-api Re-mapped Settings](https://docs.google.com/spreadsheets/d/111t6f4V3eCVkaKoBIxXi54_HlkPT-54qKShfRjl-iMs/edit#gid=2101280441).

With the exception of the authentication callbacks that will need to change to the new, environment-specific *platform-api.va.gov hostnames, all settings that required changes have been updated and tested with the help of affected teams. See **Release Steps** for details on updating authentication callbacks.

**Side Note**: In some cases, vets-api code makes subsequent Lighthouse API requests to iterate over paginated data. It is important to ensure that vets-api makes these additional request using the same API URL defined in the settings file (i.e. the forward proxy address and port) rather than directly using the API links provided as part of the API response. This might require some code changes in vets-api that swap out the host portion of the API address to the one defined in the settings.


#### **Vets-api: Synthetic Tests**
Datadog synthetics were added to test a selection of updated host address settings in vets-api.

Example: The `/facilities_api/v1/va` endpoint in vets-api is used by the va.gov Facility Locator app to make an upstream request to the Lighthouse Facilities API. We are rerouting the request to use the forward proxy instead of the api.va.gov hostname. The Datadog test confirms that the vets-api endpoint returns a success response and contains the expected data elements after rerouting the upstream request via the forward proxy. (See: [Rerouting upstream requests from vets-api](https://vfs.atlassian.net/wiki/spaces/ECP/pages/2220785993/2022-06-06+Reroute+vets-api+upstream+requests+to+Lighthouse).)

Reference: [Complete list of Datadog Tests](https://vagov.ddog-gov.com/synthetics/tests?query=tag%3A%28%22team%3Acit%22%29)


#### **Vets-website: Change \*api.va.gov References in Unit and Integration Tests**
We have updated `api.va.gov` references to `platform-api.va.gov` in the test files of the vets-website repository. These updates to the test references did not break any unit or integration tests, as they were in many cases part of canned API responses and not used in any test assertions.

Certain test references to `sandbox-api.va.gov` and `api.va.gov` did not require any changes because these reference endpoints will still be served by the Lighthouse API. **Example**: `health-care-questionnaire` application (not used in Production.)

Parameterizing the API URL was done where possible. Setting the API_URL constant as an environment variable will ensure that unit specs that check for the variable value will pass before and after changing the configured API URL to the new *platform-ap.va.gov hostname.

To prevent the future use of *api.va.gov in test files, the Release Tools Team should add custom linting rules that instruct users to use the new API URLs.
&nbsp;
&nbsp;
#### **Diagram: New Production State with vets-api in BRD and Lighthouse/Kong**

![Prod-release-state-BRD-Kong](https://user-images.githubusercontent.com/1426601/187307838-1ba75418-49e9-479c-8c85-89e8117751eb.png)
&nbsp;
&nbsp;
#### **Diagram: Future Production State with vets-api in EKS and Lighthouse/Apigee**

![Prod-release-state-EKS-Apigee](https://user-images.githubusercontent.com/1426601/187308803-f87cd357-d3ba-497a-94c6-ea792769ee6a.png)
&nbsp;
&nbsp;
### Pre-Release Tasks

#### **Coordination**
  - Kickoff Meeting to discuss team roles and responsibilities:
    - Release Plan timeline.
    - Rollback plan.
    - Notification strategy:
      - Team notifications.
      - User notifications for potential login availability.
    - Downtime window.
      - In order to minimize disruptions to Staging and Production, a coordinated release of both applications should consider the different build and deploy methods and runtimes.
      - Based on historic runtimes, determine the optimal timing for triggering the deployment of both vets-api and vets-website so that they finish at approximately the same time.


#### **VA**
   * Submit required notifications to the VA so that entities, .e.g., the Help Desk, are aware of any planned downtime or interruption of services.


#### **Site Reliability Engineering**
   * SRE playbooks prepared to use the *platform-api.va.gov hostname.


#### **Release Tools Team**
  - Adds linting rules to prevent use of the api.va.gov hostname.


#### **GIDS (GI Bill Data Services)**
   * Edits global `link_host` variable to use environment-specific *platform-api.va.gov value in both BRD and EKS deployment configurations.


### Release Steps
Due to the interdependence of API calls made from VA.gov and authentication cookies set by vets-api, the deployment of the new *platform-api.va.gov hostname will require the coordinated releases of vets-api and vets-website. 

All systems will need to be deployed and tested in Staging prior to a Production deployment.


#### **Proposed Release Sequence:**

**Step 1** (Rollback preparation)
  - Identity Team works with IAM authentication partners to prepare a rollback plan for reverting auth callback changes. 
  - Release Tools Team prepares a rollback PR and build.

**Step 2** (Coordinated release of vets-api and vets-website repositories in lower environments)
  - Broadcast an internal notification that Staging may be disrupted.
  - Site Reliability Engineering notified of impending deployment to Staging.
  - Infrastructure On-Call Team notified of impending deployment to Staging.
  - In parallel:
    - The IAM team updates their own API callback URLs for Dev and Staging.
    - The Identity Team updates the vets-api authentication URLs for Dev and Staging. 
    - The following authentication settings will need to be updated from *api.va.gov to *platform-api.va.gov:
      - `idme > redirect_uri`
      - `logingov > inherited_proofing_redirect_uri`
      - `logingov > redirect_uri`
      - `saml_ssoe > callback_url`
  - Release Tools Team updates globally configured API URL for vets-website for Dev and Staging.
  - vets-website repo:
    - PRs merged into the main branch are automatically built and deployed to both dev and staging. 
    - The GHA CI workflow runs. Completion time can vary, ~15 to 40 min.
  - vets-api in Dev (EKS):
    - Changes to the settings file are automatically deployed by ArgoCD. Completion time is fast.
  - vets-api in Staging (BRD): 
    - The Jenkins-led BRD cycle is triggered by PR merge to master, or manually if the change is initiated from the devops repo.
    - The build step takes ~30 minutes, but can be run separately if needed.
    - The deploy step is fast.
  - Based on Pre-Release coordination research, trigger the deployment of both vets-api and vets-website so that they finish at approximately the same time. **Note**: Authentication flows may not work correctly until both applications have been fully deployed.


**Step 3** (QA in Staging)
  - Once the vets-api and vets-website Staging deployments are complete, Identity Team, Release Tools Team, and other affected front-end teams QA/UAT for issues. 
  - Outstanding issues are fixed and re-deployed. Iterate as needed until Staging is stable.
  - Once Staging is confirmed to be stable, prepare the Production deployment.


**Step 4** (Coordinated release of vets-api and vets-website repositories in Production)
  - Broadcast an internal notification that Production may be disrupted.
  - Enable a sitewide, public-facing downtime notification informing users that Sign In will be unavailable.
  - Site Reliability Engineering notified of impending deployment to Production.
  - Infrastructure On-Call Team notified of impending deployment to Production.
  - In parallel:
    - The Identity Team prepares a PR for updates to the vets-api authentication URLs for Production.
    - The IAM team updates the API callback URLs.
  - Release Tools Team prepares a PR for updates to globally configured API URL for vets-website for Production.
  - The scheduled daily auto-deploy is disabled for vets-website and vets-api so that the deployment process can be fully controlled.
  - Identity Team manually runs only the build and release steps for vets-api.
  - Release Tools Team triggers vets-website deploy manually. The built-in pause in the manual release will need to be approved to resume the deployment after the Build and Release steps are finished.
  - Based on Pre-Release coordination research, trigger the deployment of both vets-api and vets-website so that they finish at approximately the same time.
  - All teams QA/UAT Production for issues.  

**Coordinated Production Release Flow**

![staggered-deploy](https://user-images.githubusercontent.com/30317/186273089-4d101052-0166-4fde-aef5-d29214b866b2.png)


### Post-Release Tasks

#### **Site Reliability Engineering**
  - SRE playbooks updated to use the *platform-api.va.gov hostname.


#### **Release Tools Team**
  - Search and replace documented instances of `api.va.gov` API references with `platform-api.va.gov`, e.g., README files, script files, code comments, etc.


#### **Console Services**
  - Adds linting rules or triggered Github action to prevent use of the *api.va.gov hostname.


#### **Mobile Flagship App**
Because the Mobile Flagship App is not bound by the same authentication workflows as vets-api and vets-website, it can be deployed separately.
  - The global configuration variables for new versions of the Mobile Flagship app are edited to use *platform-api.va.gov hostname. 
  - The global configuration variables for older versions of the Mobile Flagship app will continue to use the api.va.gov hostname and will be routed via the Lighthouse gateway.


#### **Lighthouse**
At this time the Lighthouse team is working to transition to using the new Apigee Gateway (timeline TBD.) Until that time, Kong will continue to serve as its API gateway and will honor requests to `api.va.gov/services/*` and `api.va.gov/internal/*`.
  - Once implemented, the new Apigee gateway will continue to honor requests to `api.va.gov/mobile/*` for older versions of the Mobile Flagship app.
  - Once implemented, the new Apigee gateway will continue to honor requests to `api.va.gov/vanotify` and route them to the VANotify service until that team agrees to retire the endpoint.
  - Kong Team will need to update routing to vets-api as it moves into EKS environment in different environments.


#### **Infrastructure Team**
  - Removes api.va.gov references from the revProxy. This should only happen once Apigee has been successfully set up as a new API gateway and fully owns the api.va.gov domains.
  - While the revProxy still handles the api.va.gov domain, ensures that new rules added to the `nginx_api_server.conf` file get replicated in `ngix_new_api_server.conf`.
  - Prevents new references to api.va.gov to be added to the vets-api config files `*-settings.local.yml.j2` and their EKS counterparts. 
  - **Note:** At this time only the Dev website is in EKS. As other environments migrate to EKS, routing configurations will need to be updated. The Kong team will need to be alerted of the move to EKS, so that they can update their own routing from Kong to vets-api.

    Reference: [Enabling new *platform-api.va.gov domains in EKS](https://vfs.atlassian.net/wiki/spaces/CLOUD/pages/2263351375).
  - Schedule annual TLS/SSL cert renewals for all platform-api subdomains.


#### **Documentation**
  - The Platform Content Team coordinates with contributing teams to edit documentation to use the *platform-api.va.gov hostname.
  - The Console UI Team coordinates with contributing teams to edit documentation to use the *platform-api.va.gov hostname.


## **Risks**
  - **Lack of parity in environments and deployment processes:** Consideration should be given to completing the migration of all environments to EKS prior to rolling out *platform-api.va.gov.
    - Reduces complexity in Release Plan processes.
    - Reduces complexity in Post-Release issue resolution.
  - **Staging downtime:** A window of time will be required to allow teams to test and mitigate any unforeseen issues. Authentication flows and day-to-day development tasks may be interrupted.
  - **Production downtime:** There may be a short period of downtime or instability in authentication flows.
  - **Unidentified issues:** Issues that are not identified in the Staging release may affect Production.


## **Appendix**
  - [Forward Proxy Addresses](https://vfs.atlassian.net/wiki/spaces/CLOUD/pages/2263449831/Guide+API+Hostname+Status) (Confluence)
  - [Hostname Specifications](https://vfs.atlassian.net/wiki/spaces/CLOUD/pages/2283339976) (Confluence)
  - [Reroute vets-api upstream requests to Lighthouse](https://vfs.atlassian.net/wiki/spaces/ECP/pages/2220785993/2022-06-06+Reroute+vets-api+upstream+requests+to+Lighthouse) (ADR/Confluence)
  - [Datadog Tests](https://vagov.ddog-gov.com/synthetics/tests?query=tag%3A%28%22team%3Acit%22%29) (Datadog Link)
  - [Remapped vets-api settings](https://docs.google.com/spreadsheets/d/111t6f4V3eCVkaKoBIxXi54_HlkPT-54qKShfRjl-iMs/edit#gid=2101280441) (Google Sheet)
  - [Enabling new *platform-api.va.gov domains in EKS](https://vfs.atlassian.net/wiki/spaces/CLOUD/pages/2263351375) (Confluence)
  - [New Domain to Serve vets-api](https://vfs.atlassian.net/wiki/spaces/ECP/pages/2221572288/2022-06-06+New+domain+to+serve+vets-api) (ADR/Confluence)
  - [Original Explainer Doc on VA.gov <=> Lighthouse overlap](https://vfs.atlassian.net/wiki/spaces/ECP/pages/1907523684/Explainer+-+VA.gov-Lighthouse+Overlap) (Confluence)
  - [RFC on domain name decision](https://github.com/department-of-veterans-affairs/va.gov-team/discussions/39068) (Github)
