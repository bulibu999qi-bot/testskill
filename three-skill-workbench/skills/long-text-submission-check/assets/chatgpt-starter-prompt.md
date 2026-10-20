# ChatGPT Starter Prompt

Attach this skill folder, or attach `SKILL.md` and both files under
`references/`, then send:

```text
Use the attached long-text-submission-check skill for this Web form.

Target URL: <URL>
Form or page: <page>
Account role: <role>
Normal values for unrelated required fields: <values or source>
Forbidden actions: delete, approve, publish, bulk change, and modify existing data

Inventory every visible, enabled, editable text-entry field in the scoped form,
including short name and title fields. Record excluded controls and reasons.
Then test every eligible field separately with its own exact 799-character
payload and measure the value actually retained by the control. Never place
long payloads in multiple fields at the same time. Do not run general
exploratory testing.

After freezing the field inventory and before Submit, Save, Confirm, autosave,
or any server-side write, list the eligible fields and maximum record count.
Ask me to reply with “允许提交全部字段”, “仅允许提交：字段名列表”, or
“不允许提交”. Submit at most once per authorized field. Use a clean form and
only one long-text target per submission.

For each successful submission, reopen the created record and compare the
persisted content and length with the original field-specific payload. Return
the report defined by the skill, including the complete field inventory,
screenshots, URLs, visible messages, maxlength or counter information, and
per-field source, retained, and persisted lengths.
```

If the ChatGPT session has no interactive browser capability, it must state
that the check was not executed. It may provide an execution checklist, but it
must not claim observed results.
