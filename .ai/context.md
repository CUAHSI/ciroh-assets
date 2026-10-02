# Session context

Read this first when resuming work. Keep it current (see `AGENTS.md`, "Session context").

## Project purpose

Build a set of AI skills that enable scientists to build hydrologic model simulations.

## Current goal

Bootstrap agent guidance for the repo.

## Status

- [x] Created `AGENTS.md` (Coding Principles, Session context, Adding Future Context).
- [x] Created this file.
- [x] Recorded the project purpose (user-provided).
- [ ] Skill scope, format, and layout are not yet defined. Ask the user.

## Decisions

- Session context for resuming work lives in `.ai/` (user request). The entry point is `.ai/context.md`.
- `AGENTS.md` was deliberately trimmed to Coding Principles, Session context, and Adding Future Context. Do not re-add the removed sections (status, tooling, commands, layout, conventions, licensing, git) unless asked.

## Open questions

- Which hydrologic models or modeling workflows should the skills target (e.g. SWAT, MODFLOW, WRF-Hydro, HEC-HMS, SUMMA)?
- Which agent platform and skill format (e.g. `SKILL.md` folders as in `~/.agents/skills`) should the skills use?
- Who are the target users, and what workflow stages should be covered (data acquisition, setup, calibration, evaluation, documentation)?
- Which Python tooling (package manager, test runner, linter), if any, is needed?
- `AGENTS.md` says agents must ask before editing files, but also says to update `.ai/context.md`. Is `.ai/` exempt from the ask-first rule? (Unanswered; I have been updating `.ai/context.md` as the user's earlier instruction to save context.)

## Notes

- Repo state: `LICENSE` (GPLv3), `.gitignore` (Python template), `AGENTS.md`, `.ai/`. One commit on `main`. Nothing committed since.
- Existing skills at `~/.agents/skills` (e.g. `hpc`, `ospool`, `omfa`, `omfb`, `fair`, `document`) are outside this repo. They may be useful reference for skill format.
