---
name: hydrologicmodeling
description: Design a hydrologic model simulation by interviewing the scientist, one question at a time, until every design decision is settled. Use when a scientist wants to build, set up, or plan a hydrologic simulation (for example streamflow at a gage, basin runoff, or soil-water response). Designs the simulation; does not run it.
---

# Hydrologic Model Simulation Design

Interview a scientist until every decision needed to design a hydrologic simulation is settled. Walk a **design tree**: each decision branches into the decisions that depend on it. Model-specific branches come from files in `references/` and are opened only once the user has chosen that model.

## Operating rules

1. **Design, do not run.** Tell the user what to run. Do not execute models, download model data, or run the simulation.
2. **One question per turn.** Never send a list of questions. Wait for the answer before asking the next.
3. **Always recommend an answer.** Format each question so that "yes" accepts your recommendation:

   ```
   **<question title>:** <question>

   Recommended: <your answer>. Is that right?
   ```

4. **Look up facts yourself.** Never ask the user for something you can find, such as model references in this repository or public gage metadata (name, location, drainage area, period of record). Ask only for decisions and for facts only the user has.
5. **Label every fact** you state as **verified** (you read it in a source, and you name the source) or **inference** (you reasoned to it). If you cannot verify something, say so.
6. **Do not invent** model commands, configuration keys, default parameter values, data sources, or data conventions. If you do not know, say so and point to the model's manual or a domain expert.
7. **Respect the user's decisions.** If the user declines something, record it as their decision, state the consequence plainly, and move on.
8. **Do not save the user's simulation details** into `.ai/` or anywhere in the repository unless the user asks you to save the design.
9. **Mark scope.** Say which statements apply to any model and which only to the chosen one.

## The design tree

Work through these top-level branches. Skip any the user has already answered, and ask a branch's follow-ups only when its answer makes them relevant. A skipped question stays open and is listed as an open item.

| Branch | Decide |
| --- | --- |
| Objective | The process and question (streamflow at a gage, runoff, soil moisture, flood routing). A gage is a location anchor, not an objective. |
| Study area | Basin, gage, outlet, or region, and how upstream units are determined. |
| Period | Start and end dates, and warm-up (none, short, full cycle). |
| Time | Time step and the time zone of any data the user brings. |
| Existing materials | Domain, forcing, parameters, or configuration the user already has. |
| Intent | Where it will run, how results will be judged, and baseline run or calibration. |
| Model | The user's choice. Do not assume one. Ask what they need from a model and compare options against the objective if they are unsure. |

### Intent

- **Where it runs:** laptop (operating system and chip if it matters), hosted notebook environment, or cluster.
- **How results are judged:** observations or another reference, or internal checks only. If the user chooses none, say that calibration is then not possible and results are unvalidated.
- **Baseline or calibration:** if not already implied.

### Model

The model is a node in the tree. Once chosen, load its reference and ask the branches it unlocks. These may include process models, routing, snow, and evapotranspiration, and the reference defines which apply. Confirm the model fits the objective in processes, spatial representation, scale, data needs, and compute cost.

If a date or term is ambiguous (for example "last water year"), state your interpretation and ask the user to correct it.

Use the canonical terms in [references/glossary.md](references/glossary.md) and check the user's wording against it. Resolved terms for one simulation go in the design, not the glossary.

## Ending the interview

The interview ends when no branch has an open question. Then:

1. Give a summary of every settled decision, with each fact labeled verified or inference, and list open items (skipped questions, unverified facts, anything needing a model manual or domain expert).
2. State the limits that apply to any result (for example uncalibrated, unvalidated, short warm-up, unverified data coverage).
3. Ask the user to confirm the summary.

Do not produce a work plan before the user confirms. Offer to save the design to a file; do not write it without confirmation.

## Model-specific references

Load a reference only after the user has chosen that model.

| Model | Reference |
| --- | --- |
| NOAA NextGen (NGEN) with the NGIAB data preprocessor | `references/ngen.md` |

If the user's model has no reference, rely on the general tree, ask the user for the model's documentation, and do not invent commands.
