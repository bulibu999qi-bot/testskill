# Long Text Check Rules

## Field Inventory

Inventory every visible, enabled, editable control inside the scoped form that
accepts ordinary text:

- Text inputs with `type=text` or no explicit type.
- Multiline text areas.
- Rich-text or `contenteditable` areas.

Include short text-entry fields such as names, titles, subjects, codes, reasons,
remarks, descriptions, and comments. A short intended purpose is not by itself
a reason to skip the field.

Exclude and report the reason:

- Password, verification-code, and search inputs.
- Phone, email, URL, date, and numeric inputs.
- File uploads and person or department selectors.
- Hidden, disabled, read-only, or non-text controls.
- Credential, token, secret, or other sensitive fields.
- Search, filter, and query controls outside the submitted form.

Assign stable IDs `F01`, `F02`, and so on in visible order. Record field label,
accessible name, control type, selector or stable locator, declared
`maxlength`, visible counter, required state, and eligibility decision.

After normal valid selections reveal conditional fields, inventory those newly
visible fields too. Do not enumerate every possible conditional branch unless
the user explicitly requests it. Freeze the inventory before execution and do
not silently omit fields that later become blocked.

## Field-Specific Test Data

Create one unique marker per eligible field:

`[AI-799-FNN-YYYYMMDDHHmmss]`

Replace `FNN` with the field ID. Append the Chinese character `测` until the
complete payload is exactly 799 Unicode characters. Count the marker as part of
the 799 characters and verify the source length programmatically before input.

Paste the payload in one operation when the browser tool supports paste.
Otherwise use direct fill and note the input method in the report.

After input, inspect the actual retained value:

- Use `value` for normal inputs and text areas.
- Use the editable text content or editor data model for rich-text areas.
- Record declared `maxlength`, displayed counters, and actual retained length.
- Compare the retained content with the source, not only its length.
- Do not assume the payload was accepted because the input action completed.

## Isolated Execution

Use this sequence for every eligible field:

1. Open or restore a clean copy of the same form.
2. Confirm the field is still visible, enabled, and editable.
3. Fill unrelated required fields with normal valid test data.
4. Insert the field-specific 799-character payload into only that field.
5. Measure the actual retained content and length.
6. Record validation, truncation, responsiveness, and visible data loss.
7. Capture evidence, then clear, cancel, or reopen the form.
8. Continue until every inventoried field has a result or skip reason.

Never leave 799-character payloads in multiple fields at the same time. This
keeps failures attributable to one field.

If input triggers autosave, treat the input itself as a server-side write. Do
not enter the payload until the field is covered by explicit permission.

## Submission and Persistence Phase

After input-only checks, request one scoped authorization for the frozen field
inventory. For every authorized field:

1. Open a clean form.
2. Fill unrelated required fields with normal valid test data.
3. Insert only that field's 799-character payload.
4. Confirm the retained content and length.
5. Submit or save once.
6. Record the response, URL, visible message, and screenshot.
7. Reopen the created record's detail or edit view.
8. Measure the persisted value and compare its exact content with the source.
9. Return to a clean form before the next field.

If the saved record cannot be reopened, classify persistence as Unable to
determine for that field rather than Passed.

## Evidence Standard

Capture for the inventory:

- Total visible editable controls.
- Eligible field count and ordered field list.
- Excluded field count, field names, and reasons.
- Conditional fields discovered on the normal valid path.

Capture for every eligible field:

- Page URL before and after submission.
- Field ID, label, accessible name, control type, and stable locator.
- Source payload marker and length.
- Retained length before submission.
- Persisted length after submission, when authorized and reachable.
- Declared `maxlength` and visible counter or limit guidance.
- Validation, success, or error message.
- Screenshots before submission and after the final observable result.

Classify a problem as Confirmed only when the evidence is reproducible without
another submission, or when the single submitted record directly proves data
loss, corruption, misleading success, or a violation of an explicit rule.
Otherwise use Suspected or Unable to determine.

## Safety Boundaries

- Never perform a server-side write without explicit field-scoped permission.
- `允许提交全部字段` covers only the frozen inventory and at most one
  submission per eligible field.
- `仅允许提交：字段名列表` covers only the named fields and at most one
  submission per named field.
- Plain `允许提交` is ambiguous for a multi-field batch; ask the user to choose
  the scope.
- Never alter an existing business record.
- Never delete, approve, publish, or perform bulk operations.
- Do not test sensitive, identity, contact, selector, or credential fields.
- Do not expand into general exploratory, navigation, wording, filter, API,
  performance, or multi-role workflow testing.
