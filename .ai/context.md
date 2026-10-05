# Session context

Read this first when resuming work. Keep it current (see `AGENTS.md`, "Session context").

## Project purpose

Build a set of AI skills that enable scientists to build hydrologic model simulations. Provide high-level guidance plus model-specific guidance for specific models and frameworks.

## Current goal

Develop and refine the model-agnostic hydrologic simulation skill, with NGEN as the first model-specific reference.

## Status

- [x] Created `AGENTS.md` (Project Purpose, Coding Principles, Session context, Adding Future Context).
- [x] Distilled the NGEN notebook into a model-agnostic workflow.
- [x] Converted the workflow into a real skill: `skills/hydrologicmodeling/SKILL.md` (frontmatter `name: hydrologicmodeling`, matching the directory).
- [x] Moved NGEN-specific content into `skills/hydrologicmodeling/references/ngen.md`.
- [x] Updated `skills/README.md` to list the skill.
- [ ] Test the skill by running a real intake conversation and check it follows its operating rules.
- [ ] Add model-specific references for other models as the user chooses them.

## Decisions

- Session context for resuming work lives in `.ai/`; the entry point is `.ai/context.md`.
- Finished skills live in the repository-root `skills/` directory, each in its own subdirectory. Do not store skill documents under `.ai/`.
- Do not save context from executing the skills (for example a user's simulation details). Only save information about developing the repo (`AGENTS.md` rule).
- `AGENTS.md` was deliberately trimmed. Do not re-add removed sections (status, tooling, commands, layout, conventions, licensing, git) unless asked.
- Skill design rules, set by the user in the skill: design simulations and do not run them; do not assume the model; do not produce a work plan before all needed information is gathered; ask one question at a time.
- The skill file format is `SKILL.md` with `name` and `description` frontmatter; `name` must match the directory name.
- General guidance stays in `SKILL.md`. Model-specific commands and notes go in `references/<model>.md` and are loaded only after the user picks that model.

## Open questions

- Zed discovers project-local skills at `.agents/skills/<name>/SKILL.md`, not at `skills/`. Should `skills/` stay as the source of truth with a copy or symlink at `.agents/skills/`, or should skills move? (Not decided; nothing created.)
- Which other models should get a reference (e.g. SWAT, SUMMA, WRF-Hydro, HEC-HMS, MODFLOW)?
- Who are the target users, and which workflow stages need deeper guidance (data acquisition, calibration, evaluation, documentation)?
- Which Python tooling, if any, is needed?
- Is `.ai/` exempt from `AGENTS.md`'s ask-before-editing rule? (Unanswered; `.ai/context.md` has been updated under the user's instruction to save context.)

## Notes

- `resources/NOAA NGEN - Preparing and Executing an NGEN Simulation/` holds a copy of the CUAHSI notebook example (notebook, `prepare.sh`, `restore.sh`, `requirements.txt`, `notes.txt`, `img/`). Source: https://github.com/CUAHSI/notebooks (`develop` branch, `Science Examples/NOAA NGEN - Preparing and Executing an NGEN Simulation`). Downloaded 2026-10-02 via sparse git clone. The notebook is unexecuted and targets the CIROH 2i2c JupyterHub; it does not cover installing NGEN/NGIAB elsewhere.
- Known issues in the notebook's evaluation code, flagged in `references/ngen.md`: approximate unit conversion (`3.28**3`), time zone stripped without conversion, bias-duration curve divides by observed flow and sorts series independently.
- The repo's NGEN reference commands come from the notebook and have not been run or verified.
- Repo: `LICENSE` (GPLv3), `.gitignore` (Python template), `AGENTS.md`, `.ai/`, `skills/`, `resources/`. Work since the initial commit is partly uncommitted; check `git status`.
