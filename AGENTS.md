# AGENTS.md

Guidance for AI coding agents working in this repository.

## Project Purpose

The purpose of this project is to build a set of AI skills that enable scientists to build hydrologic model simulations. It should provide both high-level guidance as well as more specific guidance when asked for working with specific models and frameworks.

## Coding Principles

- Follow the Unix philosophy: write small, focused pieces of code that do one thing well and compose cleanly, rather than monolithic scripts.
- Favor simple, easy-to-understand code over clever or overly compact solutions, even if it's more verbose.
- Comments are required on non-trivial code and should explain what is being done at a high level (intent/purpose), not restate the code line-by-line.
- Agents must always ask the user before editing files directly; do not make direct edits without explicit confirmation first.
- Do not create new git branches unless the user explicitly tells you to.

## Session context (`.ai/`)

Agents must persist any context needed to resume work in a later session in the `.ai/` folder. Agents should only save information related to the development of content within the repository, they should not save contexts when executing the skills in the repository.

- **At the start of a session:** read `.ai/context.md` (if it exists) and any files it references before doing other work.
- **During and at the end of a session:** update `.ai/context.md` with anything a fresh agent would need to continue. This includes:
  - the current goal and task status (done / in progress / next)
  - key decisions and the reasons for them
  - open questions or blockers
  - important file paths, commands, and findings that were expensive to discover
  - anything the user asked to be remembered
- **Larger notes:** put them in separate files under `.ai/` (for example `.ai/notes/<topic>.md`) and link them from `.ai/context.md`.
- **Keep it current:** edit or remove stale entries instead of only appending. Keep entries short and factual.
- **No secrets:** never write credentials, tokens, or private data into `.ai/`. Its contents are tracked by git unless the user says otherwise.

## Model reference files

Every model-specific reference lives in `skills/hydrologicmodeling/references/models/<model>.md`. Use the structure of `cfe.md` and `sloth.md` for all models. Use these sections in this order, and keep the same heading names:

1. **Title and intro.** `# <MODEL> reference (<full name>)`, then one paragraph saying when to load the file (only after the user has chosen a design that includes the model) and which other files it supplements.
2. **Source and reliability.** The repository URL, branch, and date read. List each file actually read. List what was not read and so is not covered. Mark interpretation as **(inference)**.
3. **What <MODEL> is.** A short bullet description: purpose, language, build tooling, and whether it implements BMI.
4. **Software License.** The license name.
5. **Formulation.** Process models only. Numbered list of the processes, taken from the model documentation. Omit this for utility modules.
6. **Inputs.** Subsections as applicable: run setup, BMI input variables (table with units), forcing, configuration file keys (required, scheme-dependent, optional with code defaults), calibration parameters, and time step.
7. **Outputs.** A table of output variables with units and meaning, and a note on any unit or conversion issue (for example depth versus discharge).
8. **How to use this in a design.** Bullets of guidance for the skill: scale, baseline parameters, snow, ET, time step, platform, and anything model-specific. State consequential assumptions the user must confirm.

Do not include maturity notes, open-items lists, or build/installation instructions in these files.

Rules for these files:

- Read the code or docs before stating a fact. Do not invent parameter names, defaults, supported time steps, or platform support. Mark anything unverified as **(inference)** or state that it is unverified where it appears.
- Add a row for each new model file to the Supporting Files table in the framework reference (for example `references/ngen.md`).
- Keep each file focused on that one model. Link to other references instead of repeating them.

## Adding Future Context

- Save durable project context/notes under `.ai/` rather than scattering new markdown files across the repo.
- Update this file (`AGENTS.md`) directly whenever new standing instructions or conventions are established for this repository.
