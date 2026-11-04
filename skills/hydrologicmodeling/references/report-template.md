# Stage report template and hand-off contract

Shared by every stage skill. A stage interviews the user, then writes one report. The next stage reads that report instead of re-interviewing the user. Stage skills add stage-specific sections on top of the shared sections below; they do not copy or redefine the shared ones.

## When and how to write a report

1. Produce the report only after the user has confirmed the summary of settled decisions.
2. Offer to save it. Write the file only after a second confirmation.
3. Save to the user's project, never into the skills repository (the repository that holds these skill files).

## Report sections

Use these headings, in this order. Omit nothing; write "None" for an empty section.

1. **Title and stage.** Stage name and a one-line scope statement.
2. **Inputs.** The previous report(s) this stage read, or "None (first stage)".
3. **Settings.** A table with columns: Setting, Value, Status. Status is `settled`, `open`, or `inference`.
4. **Decisions and reasons.** One entry per decision: what was chosen, why, and whether the user accepted or changed your recommendation.
5. **Resolved vocabulary.** Terms whose meaning was settled for this simulation (for example, a user's "last water year" resolved to explicit start and end dates). Canonical terms stay in the glossaries.
6. **Stage-specific sections.** Defined by the stage skill.
7. **Open items.** Skipped questions, unverified facts, and anything needing a model manual or domain expert.
8. **Limits to state.** Limits on any result, for example uncalibrated, unvalidated, short warm-up, unverified data coverage.
9. **Flow.** A mermaid flowchart of how the stage's decisions connect. Write "None" only if the stage has fewer than two connected decisions.
10. **Settings (YAML).** The same settings as the table, for the next stage to read.

## Settings YAML

One block, identical in content to the Settings table.

```yaml
stage: <stage name>
settings:
  <setting_name>:
    value: <value>
    status: settled # settled | open | inference
```

Rules:

- Every table row has exactly one YAML entry, and the reverse.
- Use `null` for the value of an `open` setting.
- Do not add settings that are not in the table.

## Hand-off contract

What the next stage must do with a report:

- **Read first.** Read the previous report in full before asking anything.
- **Do not re-ask `settled` settings.** Use them. If one now looks wrong, state why and ask whether to change it.
- **Ask about `open` settings** when they become relevant to the current stage.
- **Re-check `inference` settings.** Verify them, or confirm them with the user, and change the status to `settled` only when verified or confirmed. Never mark a setting `settled` on inference alone.
- **Carry forward** open items and limits to state. Remove an item only when it is resolved, and say so in Decisions and reasons.
- **Changing an earlier decision.** Record the change, the reason, and the setting affected in Decisions and reasons. Tell the user that earlier reports no longer match, and do not edit earlier reports.
- **Missing or incomplete report.** If a required section or setting is missing, say so and ask the user for it. Do not guess.
