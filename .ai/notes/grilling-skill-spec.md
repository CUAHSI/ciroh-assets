# Spec: Grilling-style hydrologic modeling skill

Status: not implemented. Written instead of editing the skill. Decisions below were confirmed by the user in conversation on 2026-10-21. The proposed test seam was not checked with the user.

## Problem Statement

A scientist who wants to simulate a hydrologic system must settle many linked decisions (objective, domain, period, model, forcing, parameters, evaluation) before a simulation can be built. The current skill walks a fixed ten-step intake, forbids lists of questions, and treats the model as optional and last while also saying to ask for it first. It ends in a long design document that also covers running, verifying and packaging. The scientist gets no recommendations, vague terms go unchallenged, and answers are not checked against what the chosen model supports. The next workflow step, building the simulation, has no clean handoff.

## Solution

The skill interviews the scientist in the style of the grilling skill. It walks a design tree of decisions, asks one question at a time, and gives a recommended answer with each question so that "yes" accepts it. Looking up facts (model references, public gage metadata) is the skill's job, not the user's. It sharpens vague terms against a shared glossary and challenges answers against the loaded model references. When every branch is settled and the user confirms, it produces a simulation brief (Markdown plus a YAML block) that the build step consumes. The brief is saved only on confirmation.

## User Stories

1. As a scientist, I want each question to come with a recommended answer, so that I can accept sensible defaults quickly.
2. As a scientist, I want to be asked one question at a time, so that I am not overwhelmed.
3. As a scientist, I want questions asked in an order where earlier answers shape later questions, so that I am not asked about things that no longer apply.
4. As a scientist who is unsure which model to use, I want the skill to help me compare options against my objective, so that I can choose.
5. As a scientist who has already chosen a model, I want questions specific to that model's formulations and routing, so that the configuration is complete.
6. As a scientist, I want the skill to look up facts itself (model references, public gage metadata), so that I do not have to research them.
7. As a scientist, I want the skill to say when a fact is verified versus inferred, so that I know what to trust.
8. As a scientist, I want my vague terms ("the basin", "runoff", "the model") pinned to precise terms, so that the brief is unambiguous.
9. As a scientist, I want my choices checked against the model's documented capabilities, so that I find conflicts (for example an unsupported time step) before building.
10. As a scientist, I want the skill to flag a missing process (for example snow in a snow-dominated basin), so that the formulation fits my objective.
11. As a scientist, I want ambiguous dates such as "last water year" interpreted aloud and corrected by me, so that the period is right.
12. As a scientist, I want to skip a question and have it recorded as an open item, so that I can proceed without guessing.
13. As a scientist, I want my decision to decline something (for example evaluation against observations) recorded with its consequence stated once, so that I am informed but not pressed.
14. As a scientist, I want a warm-up explained when I am unsure, so that I can choose a length.
15. As a scientist, I want forcing data coverage near the present flagged as unverified, so that I do not plan on data that does not exist.
16. As a scientist, I want to state where I will run the simulation, so that the build step can account for platform limits.
17. As a scientist, I want to state whether this is a baseline or a calibration, so that the right parameter handling is recorded.
18. As a scientist, I want to say how I will judge the results, so that the build step knows what outputs are needed.
19. As a scientist, I want a summary of every settled decision before the interview ends, so that I can confirm the shared understanding.
20. As a scientist, I want to be asked to confirm before the skill concludes, so that nothing is silently assumed.
21. As a scientist, I want a simulation brief with a settings table, decisions with reasons, resolved vocabulary, open items and limits, so that I can review what was agreed.
22. As a scientist, I want the brief to include a structured YAML block, so that the build step reads values without parsing prose.
23. As a scientist, I want the brief saved to my project only after I confirm, so that nothing is written without my consent.
24. As a scientist, I want my simulation details kept out of the skill repository, so that my work stays private.
25. As a scientist, I want the limits to state with any result (uncalibrated, unvalidated, short warm-up) recorded in the brief, so that they carry into the results.
26. As a scientist using a model with no reference file, I want the skill to ask for that model's documentation and not invent commands or keys, so that the brief contains no fabricated facts.
27. As a scientist, I want a flowchart of components and decisions in the brief, so that I can see the configuration at a glance.
28. As the build step (agent or person), I want the brief to be complete and unambiguous, so that I can construct the simulation without re-interviewing the scientist.
29. As a skill maintainer, I want a shared glossary of hydrology and modeling terms with terms to avoid, so that the vocabulary is consistent across models.
30. As a skill maintainer, I want an updated worked example in the new format, so that behavior is demonstrated and testable by reading.
31. As a skill maintainer, I want the model-versus-intake conflict removed, so that the skill's rules do not contradict each other.

## Implementation Decisions

- Questioning follows grilling: a design tree of decisions, one question at a time, each with a recommended answer worded so "yes" accepts it. This replaces the "never send a list" rule and the fixed ten-step intake. The fixed intake survives only as a checklist of the tree's top-level branches.
- The model decision is a node in the tree, not a fixed first or last step. Choosing a model unlocks model-specific branches (formulations, routing, snow, ET) from the matching model reference. This resolves the earlier conflict between "ask which model" and the intake where the model was optional and last.
- Facts are the skill's job. It reads the model references and may look up public read-only metadata (for example gage records). It labels each as verified or inference. Decisions stay with the user.
- Challenge behavior: user answers are checked against the loaded model references and against the glossary. Contradictions are raised immediately, with a concrete scenario where useful.
- Scope ends at configuration decisions: objective, domain, period and warm-up, time step and zone, forcing, parameters, formulations and routing, evaluation intent, calibration intent, platform. The former run, verification and packaging stages shrink to an "intent" section of the brief (where it runs, how results will be judged, calibration or baseline). Detailed run, verification and packaging guidance is removed.
- End condition: the tree's frontier is empty and the user confirms the shared understanding. Only then is the brief produced.
- The simulation brief has two parts from one set of answers: readable Markdown (settings table, decisions with reasons, resolved vocabulary, open items, limits to state, mermaid flowchart) and a YAML block of settings for the build step.
- Decisions and their reasons live in the brief. No ADR files.
- A shared glossary reference of canonical hydrology and modeling terms (for example catchment, nexus, hydrofabric, forcing, warm-up, formulation, realization), each with terms to avoid. Terms resolved for one simulation go in the brief, never in the repository.
- Retained rules: design only, never run models or download model data; do not invent commands, keys, defaults or data sources; respect declined questions and record them; skipped questions become open items; keep universal and model-specific guidance separate; do not save user simulation details in the repository; offer to save the brief and write it only on confirmation.
- The worked example is rewritten to show the new format: questions with recommended answers, challenges, and the final brief with its YAML block.
- Project conventions apply: a model reference stays unchanged unless the user asks; project notes live under the repository's session-context folder; the user must confirm edits to repository files.

## Testing Decisions

- A good test checks external behavior of a full interview (the questions asked, the challenges raised, the brief produced), not the wording of the skill file.
- Proposed seam (one, highest available, not yet checked with the user): a complete interview, scripted as a transcript from a starting request to a saved brief. It covers the whole skill in one place.
- Checks on a transcript:
  - exactly one question per turn, each with a recommended answer;
  - no question asked for a fact the skill could look up;
  - a skipped question appears as an open item and is not guessed;
  - an answer that conflicts with a model reference is challenged, with the check labeled verified or inference;
  - the brief appears only after the user confirms, and a file is written only on a second confirmation;
  - the YAML block matches the settings table;
  - nothing is written to the repository.
- Prior art: the existing Logan River test run in the examples folder, a hand-run transcript of the old skill. The rewritten example serves as the new reference transcript.
- No automated test harness exists. Testing is manual, by running the skill and reading the transcript against the checks above. This matches the open "test the skill" item in the project context notes.

## Out of Scope

- Building or running the simulation. This is the next workflow step.
- Run plans, verification execution and result packaging (the former stages 5 to 7), beyond recording intent in the brief.
- ADR files.
- New model reference files and edits to existing ones.
- Moving skills to the directory some tools use for discovery, and the pending git rename cleanup.
- An automated test harness.
- Publishing the spec to an issue tracker. The user asked for a file. No tracker or triage-label vocabulary was provided.

## Further Notes

- Implementation touches four things: the skill's main instructions, a new glossary reference, the rewritten example, and the project context notes (record these decisions and mark the model-versus-intake conflict resolved).
- The project's instruction file requires confirmation before editing repository files. The user confirmed these four edits in the interview, but then asked for this spec instead of implementing them. Re-confirm before implementing.
- Open at spec time: whether the user accepts the proposed single test seam above.
