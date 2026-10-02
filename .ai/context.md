# Session context

Read this first when resuming work. Keep it current (see `AGENTS.md`, "Session context").

## Project purpose

Build a set of AI skills that enable scientists to build hydrologic model simulations.

## Current goal

Develop a model-agnostic hydrologic simulation skill, using the NGEN notebook as an initial example.

## Status

- [x] Created `AGENTS.md` (Coding Principles, Session context, Adding Future Context).
- [x] Created this file.
- [x] Recorded the project purpose (user-provided).
- [x] Distilled the NGEN notebook into a model-agnostic simulation workflow under `Skills/hydrologicmodeling/`.
- [x] Set the convention that each skill has its own subdirectory under root `Skills/`.
- [ ] Choose the target audience and skill platform/format.

## Active user simulation request (intake in progress)

The skill (`Skills/hydrologicmodeling/hydrologic-model-simulation-workflow.md`) says: ask one question at a time, do not produce a work plan until all needed information is collected, design only (do not run), and do not assume the model.

Answers so far:
- Objective: compute streamflow at USGS gage 10109000 (believed to be Logan River, Utah; not verified).
- Model: NGEN.
- Period: last water year, interpreted as WY2026 (2025-10-01 to 2026-09-30; today is 2026-10-02). - Warm-up: option B, about 3 months (2025-07-01 to 2025-09-30), excluded from evaluation. - Time step: hourly.
- Evaluation: user does NOT want to evaluate against observed flow at gage 10109000, and has no alternative evidence. Plan for internal checks only (plausible ranges, forcing timing, water balance) and state this limit in the design. Implication: no calibration is possible, so this is an uncalibrated baseline run - Existing data: none. Starting from nothing (domain, forcings, and configuration all need to be created).
- Runs on: a Mac laptop. The repo's NGEN notebook targets the CIROH 2i2c JupyterHub, so Mac install steps are NOT covered by it and must come from NGEN/NGIAB documentation.

Intake status: user chose to skip the Mac chip / Docker question. Intake treated as complete; a work plan was produced in chat with the Mac install step marked pending. Plan not saved to a file (AGENTS.md: ask before editing).

## Decisions

- Session context for resuming work lives in `.ai/` (user request). The entry point is `.ai/context.md`.
- Finished, reusable skills belong in the repository-root `Skills/` directory, each in its own subdirectory. Do not store skill documents under `.ai/`.
- `AGENTS.md` was deliberately trimmed to Coding Principles, Session context, and Adding Future Context. Do not re-add the removed sections (status, tooling, commands, layout, conventions, licensing, git) unless asked.

## Open questions

- Which hydrologic models or modeling workflows should the skills target (e.g. SWAT, MODFLOW, WRF-Hydro, HEC-HMS, SUMMA)?
- Which agent platform and skill format (e.g. `SKILL.md` folders as in `~/.agents/skills`) should the skills use?
- Who are the target users, and what workflow stages should be covered (data acquisition, setup, calibration, evaluation, documentation)?
- Which Python tooling (package manager, test runner, linter), if any, is needed?
- `AGENTS.md` says agents must ask before editing files, but also says to update `.ai/context.md`. Is `.ai/` exempt from the ask-first rule? (Unanswered; I have been updating `.ai/context.md` as the user's earlier instruction to save context.)

## Notes
- `Skills/hydrologicmodeling/hydrologic-model-simulation-workflow.md` is the initial outline for a general hydrologic simulation skill. It covers objective/domain definition, input preparation, configuration, execution, verification/evaluation, and reproducibility. It is grounded in the NGEN tutorial but deliberately avoids making NGEN-specific tools universal requirements.

- `resources/NOAA NGEN - Preparing and Executing an NGEN Simulation/` holds a copy of the CUAHSI notebook example (notebook, `prepare.sh`, `restore.sh`, `requirements.txt`, `notes.txt`, `img/`). Source: https://github.com/CUAHSI/notebooks (`develop` branch, `Science Examples/NOAA NGEN - Preparing and Executing an NGEN Simulation`). Downloaded 2026-10-02 via sparse git clone as reference material for the skills.

- Repo state: `LICENSE` (GPLv3), `.gitignore` (Python template), `AGENTS.md`, `.ai/`. One commit on `main`. Nothing committed since.
- Existing skills at `~/.agents/skills` (e.g. `hpc`, `ospool`, `omfa`, `omfb`, `fair`, `document`) are outside this repo. They may be useful reference for skill format.
