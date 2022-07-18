# RFC: Keycloak user group membership management

* Comment Deadline: `2022-08-28`
* Team Crew and Name: Platform/Access Management Team
* Authors:
  * [Ritesh Patel](https://github.com/iriteshp)
* [Original RFC Pull Request](--)

## Background
Groups in Keycloak allows us to manage a common set of attributes and role mappings for a set of users. Users can be member of zero or more groups in Keycloak. Currently, if user requests AMT to be added in the group via Github issue AMT team adds user manually to the group in Keycloak. We are proposing to automate this process via GitHub Actions. 

## Motivation
1. To make the process of adding user to the group in Keycloak automated so less manual intervention is needed.
1. To maintain user memberships automatically so developers can instantly gain access to the applications they are requesting.

## Design
The below steps are currently proposed implementation to add users to the groups:

1. User fill outs the issue from in the integration repo with email, team name, group name and access type(admin, editor, viewer etc.).
1. GitHub action triggers on submission, and it adds user email to properly structured yaml file in DevOps repo.  Terraform user group membership module uses this yaml for group membership configuration.
    ```yaml
        dev:
            user_group_memberships:
                amt:
                admins:
                    - user@adhocteam.us
                    - user1@adhocteam.us
                editors:
                    - user2@adhocteam.us
                viewers:
                    - user3@adhocteam.us
    ```
1.  When GitHub actions detect the change in the user config YAML file, the action continues and plans the terraform changes. It also adds a comment with the format and plan status for the reviewers.
1. When approved, the developer from the AMT team will apply the changes and groups will be updated in the Keycloak.

## Risks
The biggest risk with this approach is that user could get access to unintended or unauthorized groups. We are adding manual approval process to eliminate this risk. The approver will be validating user group either via rosters or by reaching out to the requesting team to confirm the access.

## Alternatives
Instead of using automation, we can continue with manual process of adding user to the groups in Keycloak. It could eat up AMT teams time if something important other than this is on the plate. Managing groups manually by different developers may lead to inconsistency.