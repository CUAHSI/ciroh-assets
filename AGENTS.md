# AGENTS.md

Guidance for AI coding agents working in this repository.

## Project Purpose

The purpose of this project is to build a set of AI skills that enable scientists to build hydrologic model simulations.

## Coding Principles

- Follow the Unix philosophy: write small, focused pieces of code that do one thing well and compose cleanly, rather than monolithic scripts.
- Favor simple, easy-to-understand code over clever or overly compact solutions, even if it's more verbose.
- Comments are required on non-trivial code and should explain what is being done at a high level (intent/purpose), not restate the code line-by-line.
- Agents must always ask the user before editing files directly; do not make direct edits without explicit confirmation first.

## Session context (`.ai/`)

Agents must persist any context needed to resume work in a later session in the `.ai/` folder.

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

## Adding Future Context

- Save durable project context/notes under `.ai/` rather than scattering new markdown files across the repo.
- Update this file (`AGENTS.md`) directly whenever new standing instructions or conventions are established for this repository.
