# Session context

Read this first when resuming work. Keep it current (see `AGENTS.md`, "Session context").

## Project purpose

Build a set of AI skills that enable scientists to build hydrologic model simulations. Provide high-level guidance plus model-specific guidance for specific models and frameworks.

## Current goal

Split the single `hydrologicmodeling` skill into a phased workflow of linked grilling-style skills (Jira Epic CAM-1552, under Objective CAM-1053). Each phase interviews the user, then writes a templated report that is the input to the next phase. "Phase" replaced "stage" (stage also means river water level); defined in `references/glossary.md`.

Provisional phases: (1) conceptual model scenario setup (domain, period, time step, existing materials, intent, process representation from the Perceptual Model project; resource not yet provided); (2) model, framework and physics selection; (3) forcing and data; (4) compute and execution plan.

## Status

- [x] Created `AGENTS.md` (Project Purpose, Coding Principles, Session context, Adding Future Context).
- [x] Distilled the NGEN notebook into a model-agnostic workflow.
- [x] Converted the workflow into a real skill: `skills/hydrologicmodeling/SKILL.md` (frontmatter `name: hydrologicmodeling`, matching the directory).
- [x] Moved NGEN-specific content into `skills/hydrologicmodeling/references/ngen.md`.
- [x] Updated `skills/README.md` to list the skill.
- [x] Added `skills/hydrologicmodeling/references/models/cfe.md` (CFE reference, from the NOAA-OWP/cfe README, MODEL.md, INSTALL.md and `src/bmi_cfe.c`; commands not run). Inputs (BMI variables, forcing file, config keys, defaults, time step) and Outputs (15 BMI variables) were populated from `bmi_cfe.c` on 2026-10-05. `cfe.c` and `configs/` examples have not been read.
- [x] `references/ngen.md` Supporting Files table points Models to `models/cfe.md`.
- [x] Added `skills/hydrologicmodeling/references/models/sloth.md` (SLoTH reference, from NOAA-OWP/SLoTH README, INSTALL.md, CHANGELOG.md, `include/sloth.hpp`, `src/sloth.cpp`, read 2026-10-05; build not run). `ngen.md` Supporting Files table lists it.
- [x] Added `skills/hydrologicmodeling/references/models/noah-owp.md` (Noah-OWP-Modular reference, read 2026-10-05 from README, INSTALL.md, docs/changelog.md, `bmi/bmi_noahowp.f90`, `src/NamelistRead.f90`, `src/OptionsType.f90`, `run/namelist.input`). Follows the 8-section structure without maturity, open-items, or build sections. Other `src/` process modules and `parameters/*.TBL` were not read, so Formulation is from file names and option comments. `ngen.md` Supporting Files table lists it.
- [x] CAM-1497 to CAM-1499 (Epic CAM-1496, Done): shared glossary, model-agnostic glossary, grilling interview core (PRs #8, #9, #12, #13, #14).
- [x] CAM-1504 content merged (PR #15): lumped/gridded/resolution/model steps in the Model branch, `references/framework-glossary.md`, NGEN terms in `ngen.md`; `Skills/` renamed `skills/`; worked example moved to `resources/examples/`. The Jira story stays open under CAM-1552 to be reworded for the phased design.
- [ ] CAM-1552 stories: CAM-1500 challenge and escape-hatch (shared across phases), CAM-1501 report template and hand-off contract (do first; on branch CAM-1501-report-template-handoff-contract), CAM-1502 worked example per phase, CAM-1503 manual transcript test, CAM-1504 model selection as phase 2. Stories still to create: the split into phase skills, Perceptual Model content, forcing and compute stages.
- [ ] Add model-specific references for other models as the user chooses them.

## Decisions

- Session context for resuming work lives in `.ai/`; the entry point is `.ai/context.md`.
- Issues are tracked in Jira (project CAM, `acli`), not GitHub issues; GitHub is for code and PRs. See `docs/agents/issue-tracker.md`.
- The workflow is phased: one skill directory per phase, each producing a templated report consumed by the next. Challenge/escape-hatch behavior, glossaries and model references are written once and shared, not copied per phase. Next: define the report template first, then split the skill.
- Finished skills live in the repository-root `skills/` directory, each in its own subdirectory. Do not store skill documents under `.ai/`.
- Do not save context from executing the skills (for example a user's simulation details). Only save information about developing the repo (`AGENTS.md` rule).
- `AGENTS.md` was deliberately trimmed. Do not re-add removed sections (status, tooling, commands, layout, conventions, licensing, git) unless asked.
- Skill design rules, set by the user in the skill: design simulations and do not run them; do not assume the model; do not produce a work plan before all needed information is gathered; ask one question at a time.
- The skill file format is `SKILL.md` with `name` and `description` frontmatter; `name` must match the directory name.
- General guidance stays in `SKILL.md`. Model-specific commands and notes go in `references/<model>.md` and are loaded only after the user picks that model.
- All model references (`references/models/<model>.md`) follow the section structure of `cfe.md` and `sloth.md`, minus maturity notes, open items, and build/installation instructions (the user does not want those). The structure is defined in `AGENTS.md`, "Model reference files". Do not deviate without asking the user. `cfe.md` and `sloth.md` still contain the dropped sections until the user decides whether to remove them.

## Open questions

- Zed discovers project-local skills at `.agents/skills/<name>/SKILL.md`, not at `skills/`. Should `skills/` stay as the source of truth with a copy or symlink at `.agents/skills/`, or should skills move? (Not decided; nothing created.)
- Final names for the phases and skill directories, and the report file format.
- Which other models should get a reference (e.g. SWAT, SUMMA, WRF-Hydro, HEC-HMS, MODFLOW)?
- Who are the target users, and which workflow phases need deeper guidance (data acquisition, calibration, evaluation, documentation)?
- Which Python tooling, if any, is needed?
- Is `.ai/` exempt from `AGENTS.md`'s ask-before-editing rule? (Unanswered; `.ai/context.md` has been updated under the user's instruction to save context.)

## Pending cleanup (raise with the user)

- `.ai/notes/grilling-skill-spec.md` is a pre-implementation spec for the single skill; treat as historical until reconciled with the staged design.
- `resources/examples/logan-river-streamflow.md` still shows the older interview style (CAM-1502).
- `references/forcing/forcing.md` and `references/hydrofabric/hydrofabric.md` do not exist; `references/ngen/` is empty.
- A complete second run of the skill through to a design would make a good example.

## Notes

- `resources/NOAA NGEN - Preparing and Executing an NGEN Simulation/` holds a copy of the CUAHSI notebook example (notebook, `prepare.sh`, `restore.sh`, `requirements.txt`, `notes.txt`, `img/`). Source: https://github.com/CUAHSI/notebooks (`develop` branch, `Science Examples/NOAA NGEN - Preparing and Executing an NGEN Simulation`). Downloaded 2026-10-02 via sparse git clone. The notebook is unexecuted and targets the CIROH 2i2c JupyterHub; it does not cover installing NGEN/NGIAB elsewhere.
- Known issues in the notebook's evaluation code, flagged in `references/ngen.md`: approximate unit conversion (`3.28**3`), time zone stripped without conversion, bias-duration curve divides by observed flow and sorts series independently.
- The repo's NGEN reference commands come from the notebook and have not been run or verified.
- Repo: `LICENSE` (GPLv3), `.gitignore` (Python template), `AGENTS.md`, `.ai/`, `skills/`, `resources/`, `docs/agents/`.
