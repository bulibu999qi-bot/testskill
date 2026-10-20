# Long Text Submission Check Report

Use this structure for the final response.

## Check Summary

- Target URL:
- Form or page:
- Account role:
- Visible editable controls:
- Eligible fields:
- Excluded fields:
- Executed input checks:
- Submission permission: All fields / Selected fields / Denied / Not answered
- Authorized fields:
- Submission attempts:
- Records created:
- Final status: Confirmed / Suspected / Unable to determine / Passed

Do not report the overall check as Passed when an eligible field was skipped or
an authorized persistence check was not completed.

## Field Inventory

| Field ID | Field label | Control type | Required | `maxlength` or counter | Decision | Reason |
|-|-|-|-|-|-|-|
| F01 | | | | | Eligible / Excluded | |

List every visible editable control. Do not omit excluded fields.

## Per-Field Results

| Field ID | Marker | Source length | Retained length | Input result | Submitted | Persisted length | Exact match | Status | Evidence |
|-|-|-:|-:|-|-|-:|-|-|-|
| F01 | | 799 | | | Yes / No | | Yes / No / Not checked | | |

Use one row per eligible field. If a stage was skipped, state `Not checked` and
give the reason in the evidence or blocker section.

## Findings

For every non-passing field provide:

### Field ID and concise title

- Status: Confirmed / Suspected / Unable to determine
- Field label and control type:
- Declared rule:
- Test data marker:
- Source length:
- Retained length:
- Persisted length:
- Visible validation or response:
- Expected result based on visible or supplied rule:
- Actual result:
- Classification reason:
- Page URL before and after:
- Screenshot or evidence:
- Reproduction steps:

## Submission Authorization

- Inventory shown to the user:
- Maximum disclosed records:
- User response:
- Authorized field IDs:
- Unauthorized field IDs:
- Actual submission count:

## Skipped Stages and Blockers

List every field or stage skipped because of missing permission, autosave risk,
an inaccessible saved record, browser limitations, dynamic visibility, or
another specific blocker.

## Created Test Data

| Field ID | Record identifier or URL | Marker | Current state | Cleanup status |
|-|-|-|-|-|

Do not delete created data unless deletion was separately authorized.
