# RFC: Auto-Answer functionality for Support Slack-bot

* Date: `2022-07-14`
* Team Crew and Name: Platform SRE
* Authors:
  * Kerry Powell: powellkerry
* Pull Requests:
  * https://github.com/department-of-veterans-affairs/platform-support-slackbot/pull/25
  * https://github.com/department-of-veterans-affairs/platform-support-slackbot/pull/26

## Background

The Platform Support Slack-bot is a slack application linked to the vfs-platform-support slack channel. Platform users can submit a support ticket by typing `/support`, entering a description of the issue, and selecting a team and topic. Once submitted the ticket will be posted in the channel and assigned to the selected team.

Platform teams have found that a lot of requests could be answered by referencing existing documentation. As such a proposed workflow for returning this documentation to platform users through the Support Slack-bot has been developed and an initial set of documentation mappings has been created. 

Platform SRE would like comments both on the workflow and the documentation mappings.

## Motivation

Auto-answering support requests will save supporting teams time. For instance, QA Standards has requested that all requests dealing with end-to-end/cypress tests be auto answered with a link to this documentation: https://depo-platform-documentation.scrollhelp.site/developer-docs/end-to-end-testing-with-cypress

There is also an issue where users will post in the vfs-platform-support slack channel without using the slack-bot. This is problematic because supporting teams are not notified and it takes longer to receive a response. The proposed change will send a message, only to the user, to direct them to use the Support Slack-bot and provide them with a link to documentation to learn more. 

## Design

The proposed workflow is as follows:
1. The platform user submits a ticket through the Platform Support Slack-bot
3. The application could filter through a record of previous solutions, if available.
4. The application filters through the documentation mappings based on the tickets message, using keywords, or topic.
5. If there is a documentation mapping, the application will send back a threaded response to the ticket listing the documentation mappings
6. The threaded response will contain a promt to review documentation mapping and a "Do these help?" Yes/No question for determining the usefulness of documentation mappings
7. A "Yes" response closes the support request, while a "No" response proceeds to assign support staff.  

For more information see: https://vfs.atlassian.net/wiki/spaces/SRE/pages/2241396873/Automatic+Answers+and+Replies
Documentation mappings: https://vfs.atlassian.net/wiki/spaces/SRE/pages/2260336690/Auto-Answer+Mappings


## Risks

* The documentation mappings are too general, or too many hits are returned, and it causes clutter in the slack thread
* It could slow down common processes, such as requests for PR approvals (confusing these for requests on how to do a PR)
* Platform users ignore the documentation and this effort is unnecessary

## Alternatives

* The workflow could be changed so that platform users are presented with the documentation before the ticket is posted in the channel. This approach could clear up clutter in the channel, but would limit communication as there would not be a post if the user found the documentation helpful.

* The application could filter out support requests marked with Nature:"PR Review" which also have a github PR URL present.


## Diagrams

Detailed workflow and screenshots can be found here: https://vfs.atlassian.net/wiki/spaces/SRE/pages/2241396873/Automatic+Answers+and+Replies
A listing of documentation mappings can be found here: https://vfs.atlassian.net/wiki/spaces/SRE/pages/2260336690/Auto-Answer+Mappings




