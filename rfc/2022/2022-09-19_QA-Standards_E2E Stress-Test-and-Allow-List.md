# RFC: _E2E Stress Test and Allow List_

A new GitHub Actions workflow that discovers new or changed end-to-end test spec files in vets-website, and executes the tests in a loop probing for inconsistencies that might indicate the new or changed tests are flaky. This new workflow reuses code created for end-to-end test selection, and adds on some new functionality written in Javascript.

- **Date**: _2022-09-16_

- **Authors**: @Joe Tice @Peter Hill @Holden Hinkle @Curt Bonade

- **Status**: Proposed

- **Related Issue**: <https://app.zenhub.com/workspaces/quality-assurance-standards-613a3f47e06ba00013d05eed/issues/department-of-veterans-affairs/va.gov-team/46339>

- **Related Discussions**: _N/A_

## Background

End-to-end test flakiness frequently causes the vets-website continuous integration workflow to fail. In turn, CI failures waste VFS engineer time and disrupt daily deploys. The most common tactic when a test flakes out is to try rerunning the test until it passes. This is a slow, costly, and temporary solution that causes a problem later when the test fails again. Platform website contains [documentation](https://depo-platform-documentation.scrollhelp.site/developer-docs/flaky-test-management-in-cypress) that outlines appropriate proper ways to test for flakiness such as running a test in a loop, but no way to enforce that this documentation is used unless a support request is filed. Most incidents of E2E Flakiness do not result in a support request, but are just silently rerun and not spoken about. This is backed by comparing our flakiness tracking in Domo to the number of support requests we receive triggered by flakiness.

## Motivation

Solving flakiness will remove many roadblocks for numerous aspects of platform operation. Without flaky tests, engineers stand to save sometimes hours of time per task by not having to rerun a workflow that can take up to 45 minutes because a test failed unexpectedly. This would also stop unexpected hiccups in both branches merging into main as well as our production deployments. Removing flakiness would also ensure the quality of our product is tested by a stable testing suite while simultaneously eliminating support requests around flakiness-driven test failures.

## Design

Our solution resolves around a new BigQuery table called [vets_website_e2e_allow_list](https://console.cloud.google.com/bigquery?authuser=1&project=vsp-analytics-and-insights&ws=!1m5!1m4!4m3!1svsp-analytics-and-insights!2svsp_testing_tools!3svets_website_e2e_allow_list) which holds attributes of each E2E spec file including:

- its relative spec path (`spec_path`, `string`)

- whether the spec is allowed or not (`allowed`, `bool`)

- the titles of the failing tests in the spec (`titles`, `array` of `string`s)

- the date the spec file was disallowed (`disallowed_at`, `string`)

There are two parts of our solution:

1.  The `E2E Stress Test` GitHub Actions Workflow in `vets-website` that runs once a day

2.  New jobs (and new steps in existing jobs) in the `Continuous Integration` GitHub Actions Workflow in `vets-website`

### The `E2E Stress Test` GitHub Actions Workflow

The purpose of this workflow is to discover flaky E2E tests and disallow them from running in CI. This workflow runs once a day and runs all allowed E2E test specs in a loop.

Allowed specs are specs in the `vets_website_e2e_allow_list` table with an `allowed` field value of `true`. If a test fails while running in the loop, the test is deemed flaky and the spec file's `allowed` field in the `vets_website_e2e_allow_list` table is set to `false`, the `title` of the failed test is added to the `titles` array, and the `date field is set to the current `datetime`, ensuring that the test is skipped in CI until it is fixed.

The workflow runs once a day because:

1.  The more times tests are run, the greater the opportunity they have to fail if they are flaky

2.  If infrastructure changes, or if any dependencies change (e.g. versions of node, packages, including Cypress, etc.) we want to verify that changes don't introduce flakiness.

#### Key jobs and steps in the E2E Stress Test GitHub Actions Workflow

- The `fetch-e2e-allow-list` job grabs the contents of the `vets_website_e2e_allow_list` table and sets it as an `output` called `allow_list`

- The `cypress-tests-prep` job passes new env vars into Test Selection (`script/github-actions/select-cypress-tests.js`) called `ALLOW_LIST` (which is set to the `allow_list` `output` set in `fetch-e2e-allow-list`) and `IS_STRESS_TEST` which is set to `true`

- Test Selection filters the allow list by `allowed=true` and sets the new env var `TESTS_TO_STRESS_TEST` to the filtered list. This value is assigned to the `output` called `tests`.

- The `stress-test-cypress-tests` job passes a new env var called `IS_STRESS_TEST` to the `run-cypress-tests.js` script. If `IS_STRESS_TEST` is set to `true`, it runs each batch of tests in a loop.

- The `update-e2e-allow-list` job checks out the `qa-standards-dashboard-data` repo and

  - Creates and publishes a Mochawesome Report

  - Updates the `vets_website_e2e_allow_list` table, disallowing any tests that failed

  - Updates the [vets_website_e2e_allow_list_change_log](https://console.cloud.google.com/bigquery?authuser=1&project=vsp-analytics-and-insights&ws=!1m5!1m4!4m3!1svsp-analytics-and-insights!2svsp_testing_tools!3svets_website_e2e_allow_list_change_log) table, which contains a historical record of when specs are

    - `added/enabled`\*

    - `added/disabled`\*

    - `enabled`

    - `disabled`

  - Creates a new GitHub Issue on the `va.gov-team` repo for each spec file that has a newly detected flaky test

    - Using the Product Directory, we identify which product the test belongs to and retrieve the GitHub team label for the product, if the label exists

    - When the new issue is created, it's assigned to the `e2e-flaky-test` label and to the product's team label, if it exists

  - Creates an _E2E Stress Test Summary_ page that

    - provides a link to the Mochawesome Report

    - lists the tests that normally would have run because they're not skipped, but didn't because they're disallowed

Additionally, a new GitHub Actions Workflow in the `qa-standards-dashboard-data` repo called `Slack Notice for Allow List` runs once a day at 9am and sends a message to `#qas-notifications` Slack channel on the DS@VA Slack instance with a list of currently disallowed spec paths and how many days they've been disallowed for.

### Continuous Integration GitHub Actions Workflow

The purpose of the updates to this workflow are to require new and updated E2E tests to be stress-tested before they are merged into master. Additionally, the updates allow engineers to fix failing tests and have them automatically allowed again.

#### Key jobs and steps in the `Continuous Integration` GitHub Actions Workflow

- The `fetch-e2e-allow-list` job grabs the contents of the `vets_website_e2e_allow_list` table and sets it as an `output` called `allow_list`

- The `cypress-tests-prep` job passes a new env var into Test Selection (`script/github-actions/select-cypress-tests.js`) called `ALLOW_LIST` (which is set to the `output` called `allow_list` in `fetch-e2e-allow-list`

- Test Selection sets the following env vars which are then set as outputs

  - `TESTS`, the `output` is `tests`

  - `TESTS_TO_STRESS_TEST`, the `output` is `tests-to-stress-test` (these are selected tests that are new or updated tests)

  - `TEST_SELECTION_DISALLOWED_TESTS`, the `output` is `test_selection_disallowed_tests` (these are selected tests that are currently set to `allow=false`)

- The `cypress-tests` job runs tests normally (i.e. the tests are split up amongst a number of Cypress runners and are only run once)

- The `stress-test-cypress-tests` job runs `tests-to-stress-test` in a single Cypress instance, in a loop. This job runs in parallel to the `cypress-tests` job so it doesn't add any time to the workflow.

- The `update-e2e-allow-list` job checks out the `qa-standards-dashboard-data` repo and

  - Creates and publishes a Mochawesome Report

  - Updates the `vets_website_e2e_allow_list` table

    - Adds records for new spec paths

    - Updates any tests that were previously set to `allow=false` to `allow=true` if they pass the stress-test

  - Updates the [vets_website_e2e_allow_list_change_log](https://console.cloud.google.com/bigquery?authuser=1&project=vsp-analytics-and-insights&ws=!1m5!1m4!4m3!1svsp-analytics-and-insights!2svsp_testing_tools!3svets_website_e2e_allow_list_change_log) table, which contains a historical record of when specs are

    - `added/enabled`\*

    - `added/disabled`\*

    - `enabled`

    - `disabled`

  - Creates an E2E Stress Test Summary page that

    - provides a link to the Mochawesome Report

    - lists the tests that would normally have run because they're not skipped, but didn't because they're disallowed

## Risks

- Disabling entire spec files as opposed to focusing on manually disabling individual tests could result in temporarily having less test coverage than engineers handling this solution on a per-incident basis.

- Disabled specs could stay skipped longer than desired even with all we will do to try and notify the teams and encourage them to make the appropriate fixes.

## Alternatives

- We considered a manual workflow that would require the user to manually submit their test for approval to run in normal CI again. We felt that automating this process was an easier experience for users as them filling out a form was an extra step that was unnecessary.

\*_added to the vets_website_e2e_allow_list table_
