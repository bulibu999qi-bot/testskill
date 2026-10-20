---
name: long-text-submission-check
description: Check all visible editable text fields in a Web form with exact 799-character values. Use for long-text, maxlength, truncation, data-too-long, or persistence testing. Do not use for general UI testing.
---

# Long Text Submission Check

Use available browser-control, page-inspection, and screenshot capabilities to
inventory every eligible field in the scoped form, then check each field in
isolation. If these capabilities are unavailable, state that the check was not
executed and stop.

This skill has one responsibility: determine how a form handles an exact
799-character value in every visible text-entry field before submission and,
when authorized, after persistence.

When invoked through `three-skill-workbench` with `profile=long-text`, read
[workbench integration](references/workbench-integration.md). The selected core
skill remains responsible for its normal execution, evidence, and deliverables;
this skill adds only the long-text field inventory and isolated 799-character
check.

## Confirm Scope

Obtain or discover only what this check needs:

- Target URL and form or page.
- Login state and available account role.
- Normal valid values for unrelated required fields.
- Actions that must not be performed.

If the target page is inaccessible or no eligible field exists, stop and report
the blocker. Do not expand into general page exploration.

## Inventory All Eligible Fields

Read [long-text check rules](references/long-text-check-rules.md). Inventory all
visible, enabled, editable controls in the scoped form that accept ordinary
text, including short-name and title fields:

- Text inputs with `type=text` or no explicit type.
- Multiline text areas.
- Rich-text or `contenteditable` areas.

Exclude passwords, verification codes, search or filter controls, phone
numbers, email addresses, URLs, dates, numeric fields, file uploads, person or
department selectors, hidden or disabled fields, and fields intended for
secrets. Record every exclusion and its reason.

Use the fields visible in the initial form and fields that become visible during
the normal valid completion path. Do not brute-force every conditional
combination unless the user explicitly expands the scope.

Assign stable field IDs `F01`, `F02`, and so on. Freeze the inventory before
execution so the final report can distinguish planned, executed, skipped, and
newly appeared fields.

## Build One Payload Per Field

For each field, create a unique marker:

`[AI-799-FNN-YYYYMMDDHHmmss]`

Replace `FNN` with the assigned field ID. Append the Chinese character `测`
until the complete payload contains exactly 799 Unicode characters. Verify the
payload length programmatically before entering it.

## Run the Input Phase for Every Field

Test fields sequentially, never with long text in multiple fields at once:

1. Open or restore a clean copy of the same form.
2. Fill unrelated required fields with normal valid values.
3. Enter the field-specific payload in one operation.
4. Inspect the actual retained content and length.
5. Record declared `maxlength`, visible counter, validation, truncation,
   responsiveness, and visible data loss.
6. Capture evidence before clearing, cancelling, or reopening the form.
7. Continue until every inventoried field is executed or has a recorded skip
   reason.

Do not infer that all 799 characters were accepted merely because the input
action completed. If typing can trigger autosave or another server-side write,
obtain submission permission before entering the payload.

## Require Scoped Submission Permission

After the field inventory is frozen and before any server-side write, list the
eligible fields and maximum record count, then ask in Chinese:

> 当前页面共发现 N 个合格字段：字段清单。为逐字段定位问题，提交阶段最多为每个字段创建 1 条测试数据，共不超过 N 条。请选择并明确回复：
> 1. “允许提交全部字段”
> 2. “仅允许提交：字段名列表”
> 3. “不允许提交”

Only an explicit response matching one of these options authorizes the stated
scope.

- `允许提交全部字段` authorizes at most one submission per inventoried field.
- `仅允许提交：字段名列表` authorizes only the named fields, at most once each.
- An ambiguous `允许提交` does not define the batch scope; ask the user to choose
  one of the listed options.
- Do not reuse permission from another task, page, inventory, or conversation.
- If permission is absent or denied, stop before the server-side action and
  report that persistence was not checked.
- Permission to submit does not authorize deletion, approval, publishing, bulk
  changes, or modification of existing business records.

## Submit and Verify Each Authorized Field

For each authorized field:

1. Open a clean form and fill unrelated required fields with normal values.
2. Enter only that field's payload and confirm its retained value.
3. Submit or save once.
4. Record the visible response, resulting URL, and screenshot.
5. If submission succeeds, open the resulting detail or edit view without
   changing another record.
6. Locate that field's saved value and measure its persisted length.
7. Compare the persisted content with the exact source payload, including the
   unique marker.
8. Return to a clean form before testing the next authorized field.

Do not combine multiple long-text fields in one submission. Do not retry a
submission merely to reproduce an issue. Do not delete created data unless the
user separately authorizes deletion.

## Classify and Report

Classify every eligible field independently using observable evidence and
supplied requirements:

- `Confirmed`: reproduced data loss, corruption, silent truncation that violates
  an explicit requirement, misleading success, or an actionable submission
  failure caused by the long value.
- `Suspected`: behavior is abnormal but the expected limit or business rule is
  unavailable.
- `Unable to determine`: submission, persistence, or required evidence could not
  be completed.
- `Passed`: the field handled the payload according to its visible or supplied
  rule, and every authorized stage completed without data inconsistency.

A clear maximum-length rule that blocks or truncates input consistently is not
automatically a defect. Silent truncation is a risk, but classify it as
Confirmed only when an explicit requirement forbids it.

Read [report template](references/report-template.md). Report inventory totals,
excluded fields and reasons, per-field input, retained and persisted lengths,
submission scope, created records, visible messages, URLs, screenshots, and
skipped stages. Never claim that execution occurred when browser access or
authorization was unavailable.
