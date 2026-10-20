# Workbench Integration

`profile=long-text` is an optional specialist profile for
`three-skill-workbench`. It does not replace the selected core skill and does
not change that skill's default behavior when the profile is absent.

## Routing by Mode

### `mode=requirement`

Use `requirement-test-workbench` as the core skill. Analyze only supplied
requirements and produce a long-text handoff containing:

- Candidate text fields and their requirement-based names.
- Confirmed, inferred, or unknown length rules.
- Required companion values and known conditional visibility.
- Submission, persistence, and data-operation constraints.
- Open questions that block an executable check.

Do not claim that a candidate field is visible, enabled, editable, or present
in the actual page. Do not execute browser checks in this mode. Keep the
handoff separate from the core skill's formal test-case table.

### `mode=explore`

Use `reverse-test-workbench` as the browser execution, evidence, checkpoint,
and reporting core. After its entry checks and quick page map:

1. Inventory the actual visible, enabled, editable text-entry fields.
2. Reconcile page facts with any requirement handoff without treating the
   handoff as page evidence.
3. Run the isolated input phase for every eligible field.
4. If the requested scope includes submission, use the authorization contract
   in this skill before any server-side write.
5. Map results into the core skill's existing run data and evidence. Do not
   create a parallel report stack.

Without explicit submission authorization, stop after the input-only phase and
record persistence as not checked.

### `mode=acceptance`

Use `workbench-ui-acceptance-execution` as the browser execution and reporting
core. Consume executable cases or a sufficiently complete handoff. Run one
clean form and at most one authorized submission per target field, then reopen
the created record and compare persisted content with the exact source
payload.

If the supplied cases do not identify the page, normal companion values,
expected rule, or permitted fields, stop and report the missing precondition.
Keep screenshots and findings in the core acceptance report.

### `mode=workflow`

Split the responsibility across the normal workflow:

1. `requirement`: produce candidate fields, rules, constraints, and unknowns.
2. `explore`: establish the actual field inventory and execute isolated
   input-only checks.
3. `acceptance`: request scoped submission permission, then execute authorized
   per-field submission and persistence checks.

Permission is not carried between pages, inventories, or conversations.
Permission from the exploration stage may be consumed by acceptance only when
the target page and frozen inventory are unchanged and the user's response
explicitly names that same scope.

## Profile Boundaries

- `profile=long-text` and `profile=high-frequency` are mutually exclusive.
- Do not silently combine profiles or reinterpret one as the other.
- Direct `skill=long-text-submission-check` invocation may run standalone and
  uses this skill's report template.
- Profile invocation uses the selected core skill's normal deliverables.
- Never copy these rules into the three core skills; the integration belongs
  to the router and this specialist skill.
