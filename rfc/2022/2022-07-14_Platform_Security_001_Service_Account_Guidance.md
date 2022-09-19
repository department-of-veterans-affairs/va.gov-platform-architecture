# RFC: Service Account Guidance

<!--
The title is what you want comments on. Use the active voice in a future tense.
Example:
    - The website will be built using the XZY framework
-->

* Comment Deadline: `2022-08-14`
* Team Crew and Name: Platform Security
* Authors:
  * [Troy Mosher](https://github.com/troymosher)

 * [Original RFC Pull Request](https://github.com/department-of-veterans-affairs/va.gov-platform-architecture/pull/35)

## Background

Numerous service accounts exist throughout the Platform, and there is currently no central tracking of them, nor consistent guidance on how they are created, approved, managed, and maintained.

## Motivation

The proposed Service Account Guidance would provide guidance on how service accounts should be requested, approved, managed and maintained.

## Design

The proposed [Service Account Guidance](https://vfs.atlassian.net/wiki/spaces/PSEC/pages/2222588013/Service+Account+Guidance) is a document in the PSEC Confluence page.

Content below:

#### What is a Service Account?
A service account is a special type of shared/group privileged account used to execute applications, services, or other processes.  Service accounts are used to run automated or persistent processes and are intended to be used by applications, not interactively by people.

#### What unique risks do Service Accounts pose?
Service accounts are not tied back to a unique individual.   This makes tracing back actions performed by a service account to a specific individual a challenge.

Service account authenticators are often shared or known amongst a team.  This necessitates rotation of authenticators when a member of that team leaves the project.

Service account authenticators are often stored within applications or code for use, and rotation of that authenticator can cause outage or downtime if not updated in all the applications, services, or other processes that rely upon its use.

#### Platform Security Guidance for Service Accounts

*Assigned Account Manager*

Every service account should have an assigned Account Manager.  The Account Manager is a specific human individual with the overall responsibility for the service account but can delegate use of the account.  The Account Manager may be a team lead or member of the team that necessitates the use of the service account.  In the case of an Account Manager leaving, a replacement Account Manager from the team must be designated. The Team Lead is responsible for ensuring that all Service Accounts used by their team have an assigned Account Manager at all times (and one Account Manager may own multiple Service Accounts). The team Lead may work with other Team Members (and individuals outside of the team) to determine or designate the Account Manager but the responsibility for continuity of this role lies solely with the Team Lead. New Team Leads inherit this responsibility from those who came before them.

*Service Account Identifiers*

Service accounts should be named using a format that easily identifies it as a service account, includes the product name, managing team name, along with unique information about its intended purpose.  Generic names, individual accounts, or built-in administrative accounts should not be used.

Recommended naming convention:  svc-${PRODUCT}-${TEAM}-${USAGE}

**Examples:**

Good: svc-platform-infra-nightly_cron_job

Bad:  jwilson, service_account, admin

*Required Approval*

The Account Manager is responsible for initially requesting approval, via a GitHub Issue Template, for the creation and usage of a service account.  Each service account should have a valid authorization approved by the ISO or designee (OCTO-DE VA employee).  In the case of an Account Manager leaving, the replacement Account Manager (or the Team Lead of the team owning the Service Account) must submit a request for approval by the ISO or designee (OCTO-DE VA employee) designating themselves as the new Account Manager.

*Authenticator Management*

The Account Manager is responsible for the creating, maintaining, safeguarding, and rotating of the service account authenticator.

In the case of passwords, the Account Manager must ensure that the password for the service account meets minimum password requirements for length and complexity as dictated by VA policy.  Per IA-5(1), the VA requires a case sensitive 12-character password comprised of upper-case letters, lower case letters, numbers and special characters, including at least one of each category.

Authenticators should never be stored in personal password managers.  It is required that authenticators be stored in Parameter Store or an equivalent solution.

The Account Manager can grant (or revoke) access to the authenticator at their discretion amongst their team as necessary for those with a legitimate need to know.  Per IA-5, VA policy requires that service account authenticators be rotated at a minimum of every 3 years, or upon any member of the team or group with access to it leaving or no longer requiring access to the authenticator.  The Account Manager is responsible for providing access to the newly rotated authenticator as needed and ensuring that any application, service, or other process that rely on its use is updated.

*Intended Usage*

The Account Manager is responsible for ensuring the service account is only used for the intended purpose as stated in the initial request for authorization.  Service accounts shall not be used interactively by an individual or used beyond their intended scope.  If a need arises to use a service account interactively for troubleshooting, approval from the ISO or designee (OCTO-DE VA employee) must be obtained and Platform Security should be CC’ed on the request.  If a need arises to either extend the intended usage of the service account beyond the initial scope, or reduce the scope of the intended usage, a new request documenting the change in intended usage must be submitted and approved by the ISO or designee (OCTO-DE VA employee).

*Documentation and Tracking*

The Account Manager is required to document, at a minimum, the following information for service accounts they manage:

* Service account name
* Type of authenticator used, method of safeguarding and where it is stored
* Names of all specific individuals with access or knowledge of the service account authenticator
* Date of last authenticator rotation and reason (ie, 3 years, team member leaving)
* Intended usage of the service account
* All applications, services, or processes that rely on or use the service account.

The Platform Security team will collect this documentation from all Account Managers within Platform and maintain it in one location to track all service accounts.

*Offboarding*

The Account Manager is responsible for offboarding the Service Account when its use is no longer needed.



## Risks

* Existing service accounts that are already in use may not conform to this new guidance.  Would require additional effort to backfill compliance to the guidance.
* Specific guidance may potentially not be possible to achieve for some service accounts, depending on unique circumstances.

## Alternatives

* A Service Account Guidance in some form is necessary, the alternative to the currently proposed one would be changes to the content of the document based on comments and input from others.

## Diagrams

N/A

## References

Link to proposed [Service Account Guidance](https://vfs.atlassian.net/wiki/spaces/PSEC/pages/2222588013/Service+Account+Guidance) in PSEC Confluence page.


