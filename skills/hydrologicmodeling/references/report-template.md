# Phase report template and hand-off contract

Shared by every phase skill. A phase interviews the user, then writes one report. The next phase reads that report instead of re-interviewing the user. Phase skills add phase-specific sections on top of the shared sections below; they do not copy or redefine the shared ones.

## When and how to write a report

1. Produce the report only after the user has confirmed the summary of settled decisions.
2. Offer to save it. Write the file only after a second confirmation.
3. Save to the user's project, never into the skills repository (the repository that holds these skill files).

## Report sections

Use these headings, in this order. Omit nothing; write "None" for an empty section.

1. **Title and phase.** Phase name and a one-line scope statement.
2. **Inputs.** The previous report(s) this phase read, or "None (first phase)".
3. **Settings.** A table with columns: Setting, Value, Status, Basis. Status is `settled` or `open`. Basis is `user decision`, `verified` (name the source), or `inference`. If the study area includes a gage, Settings must have a `gage_id` row (the gage's identifier, for USGS the site number) and a `gage_url` row (for USGS, `https://waterdata.usgs.gov/monitoring-location/<site number>/`), in addition to the gage name. It must also have a `gage_record` row: the data type the objective uses, its record start and end (or "ongoing"), and the date it was read. If the simulation period is not fully inside the record, say so in Limits to state.
4. **Decisions and reasons.** One entry per decision: what was chosen, why, and whether the user accepted or changed your recommendation.
5. **Resolved vocabulary.** Terms whose meaning was settled for this simulation (for example, a user's "last water year" resolved to explicit start and end dates). Canonical terms stay in the glossaries.
6. **Phase-specific sections.** Defined by the phase skill.
7. **Open items.** Skipped questions, unverified facts, and anything needing a model manual or domain expert.
8. **Limits to state.** Limits on any result, for example uncalibrated, unvalidated, short spin-up, unverified data coverage.
9. **Flow.** A mermaid flowchart of how the phase's decisions connect. Write "None" only if the phase has fewer than two connected decisions.
10. **Settings (YAML).** The same settings as the table, for the next phase to read.

## Settings YAML

One block, identical in content to the Settings table.

```yaml
phase: <phase name>
settings:
  <setting_name>:
    value: <value>
    status: settled # settled | open
    basis: user decision # user decision | verified | inference
    source: <where it was read, for verified only>
```

Rules:

- Every table row has exactly one YAML entry, and the reverse.
- Use `null` for the value and basis of an `open` setting.
- A setting with basis `inference` is never `settled`.
- Do not add settings that are not in the table.

## Hand-off contract

What the next phase must do with a report:

- **Read first.** Read the previous report in full before asking anything.
- **Do not re-ask `settled` settings.** Use them. If one now looks wrong, state why and ask whether to change it.
- **Ask about `open` settings** when they become relevant to the current phase.
- **Re-check settings with basis `inference`.** Verify them, or confirm them with the user, then set the basis to `verified` or `user decision` and the status to `settled`. Never mark a setting `settled` on inference alone.
- **Carry forward** open items and limits to state. Remove an item only when it is resolved, and say so in Decisions and reasons.
- **Changing an earlier decision.** Record the change, the reason, and the setting affected in Decisions and reasons. Tell the user that earlier reports no longer match, and do not edit earlier reports.
- **Missing or incomplete report.** If a required section or setting is missing, say so and ask the user for it. Do not guess.
