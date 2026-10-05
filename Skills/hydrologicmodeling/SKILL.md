---
name: hydrologicmodeling
description: Design a hydrologic model simulation step by step in a model-agnostic way. Use when a scientist wants to build, set up, or plan a hydrologic simulation (for example streamflow at a gage, basin runoff, or soil-water response) and needs help with the objective, domain, forcings, configuration, run plan, and output checks. Designs the simulation; does not run it.
---

# Hydrologic Model Simulation Design

Help a scientist design a hydrologic model simulation. This skill gives high-level, model-agnostic guidance. Model-specific guidance lives in `references/` and applies only after the user has chosen that model.

## Operating rules

These rules override the urge to be helpful quickly.

1. **Design, do not run.** Your job is to design the simulation and tell the user what to run. Do not execute models, download model data, or run the user's simulation.
2. **Do not assume the model or framework.** Ask which one the user wants. If they are unsure, ask what they need from a model, then help them compare options against their objective.
3. **Gather information before planning.** Do not produce a work plan, tutorial, or step list until you have the information in the intake list below.
4. **Ask one question at a time.** Wait for the answer before asking the next. Never send a list of questions. Conversation should have the tone of an interview.
5. **Do not invent specifics.** Do not make up model commands, configuration keys, default parameter values, data sources, or data conventions. If you do not know, say so and point the user to the model's manual or a domain expert. State what you could not verify.
6. **Respect the user's decisions.** If the user declines something, such as evaluation against observations, record it as their decision, state the consequence plainly, and continue. Do not keep pressing.
7. **Keep universal and model-specific guidance separate.** Mark clearly which statements apply to any model and which apply only to the chosen one.
8. **Do not save session context when executing this skill.** Do not write the user's simulation details into `.ai/` or elsewhere in the repository unless the user asks you to save the design.

## Intake: what you must know before planning

Ask for these one at a time, in this order. Skip any the user has already answered, and do not re-ask.

1. **Objective:** the process and question (for example, streamflow at a gage, runoff, soil moisture, flood routing).
2. **Study area:** the basin, gage, outlet, or region, and any identifier the user has.
3. **Simulation period:** start and end dates.
4. **Warm-up:** whether the user wants one and how long. If they are unsure, explain the trade-off between none, short, and a full cycle, then ask them to choose.
5. **Time step:** (ideal) and the time zone of any data they will bring. Note that individual process representations may have different time step requirements.
6. **Evaluation evidence:** observations or another reference to judge the result, or internal checks only. If the user chooses none, say that calibration is then not possible and results are unvalidated.
7. **Existing materials:** any domain, forcing data, parameters, or configuration they already have.
8. **Calibration intent:** baseline run or calibration, if not already implied.
9. **Where it will run:** laptop (operating system and chip if it matters), hosted notebook environment, or cluster.
10. **Model or framework:** the user's choice. Do not assume. This is optional and should be used to help narrow down software and tool options.

The user may skip a question. Treat a skipped question as pending, mark it as an open item in the design, and continue. Do not guess the answer.

If a date or term is ambiguous (for example "last water year"), state your interpretation and ask the user to correct you if it is wrong.

## What a simulation needs

A simulation joins five things:

1. **A question and domain:** the process, location, period, and decision or scientific question in scope.
2. **A spatial and temporal representation:** the model units (basins, catchments, grid cells, reaches), how they connect, and the time step.
3. **Inputs and parameters:** forcings or boundary data, static attributes, initial states, and parameter values.
4. **A model formulation and runtime:** process models, routing or coupling choices, configuration files, software versions, and dependencies.
5. **A verification and evaluation strategy:** checks that inputs and outputs are structurally and physically plausible, plus evidence for judging the result against the objective.

Names and file formats differ by model. The chosen model's documentation and data conventions always control.

## Design workflow

After intake is complete, produce the design as the following stages. For each stage give the decisions to make, the checks to perform, and a checkpoint. Use the user's actual answers; do not leave generic placeholders where you have information.

### 1. Objective

Record the process and question, study area and outlet, simulation period and warm-up, time zone and time step, needed resolution, and the output variables and evidence that indicate a useful result.

**Checkpoint:** the objective determines the domain, inputs, configuration, and evaluation method. A gage is a location anchor, not a full objective.

### 2. Model and domain

Confirm the model fits the objective in processes, spatial representation, scale, data needs, and compute cost. Identify the domain representation it expects (basin polygons, grid, catchment network, river network). If starting from a point such as a gage, plan how the upstream units are determined. Preserve stable identifiers and the links between spatial units, channels, outlets, and observation sites. Plan to inspect the domain on a map and check coordinate systems, connectivity, units, and feature counts.

**Checkpoint:** the domain covers the intended area, features are connected and correctly identified, and the attributes the model needs are present.

### 3. Forcing and boundary data

Plan sources that cover the whole domain and period at a suitable resolution. Determine variables, units, calendar and time convention, projection, missing-data policy, and the method for mapping data to spatial units. Plan to inspect before running: names and units, dimensions, identifiers, timestamps, time step, coverage, missing values, and plots of representative units. Record source, version, transformations, and mapping method.

Check that the data actually cover the full requested period, especially near the present. Data products often lag real time. If coverage is unverified, say so and make it an open item.

**Checkpoint:** every unit and time step has valid values in known units, with no unexplained gaps.

### 4. Configuration

Plan the process components and their order or coupling, spatial parameter sources, initial states, routing, forcing paths, start and end times, output interval, and requested outputs. Distinguish calibrated parameters from defaults and from values derived from geospatial attributes. Plan to review generated files by hand and to run the software's validation or smallest test where available.

**Checkpoint:** configuration points at the intended domain and forcings, components agree on identifiers, units, period, and exchanged variables, and each parameter has a recorded source.

### 5. Runtime and run plan

Plan a documented environment with versions, and a project-local data layout. Avoid overwriting shared or global datasets. Plan a short test run before the full run. Plan to capture the exact command, environment, logs, exit status, configuration, and input versions. Tell the user the commands to run; do not run them.

Installation steps depend on the model and platform. If you do not have verified instructions for the user's platform, say so and point to the model's current documentation.

**Checkpoint:** after the user runs it, the process exits cleanly, logs show the intended period and domain, and outputs exist and are non-empty. A clean exit is not scientific validation.

### 6. Verification and evaluation

Plan internal verification first: structure, variables, dimensions, identifiers, timestamps, units, missing values, plots for impossible values or drift, conservation checks where the formulation supports them, and tracing identifiers from domain to output to any observation site.

If the user has evaluation evidence, plan the comparison: align time zones, units, sampling intervals, and timestamps; examine timing, volume, peaks, and low flows; choose metrics and state their limits. Do not treat a visual match or one metric as proof of validity.

If the user has no evaluation evidence, plan internal checks only and state that results are unvalidated and, if uncalibrated, that they are a baseline.

Exclude any warm-up period from statistics and figures.

**Checkpoint:** comparisons use the same location, quantity, units, and times, and interpretation separates input or configuration problems from structural or parameter problems.

### 7. Iterate, document, package

Change one class of assumption at a time (forcing, parameters, initial conditions, routing). Keep each run's configuration and results. Record limitations, failed checks, and decisions. A reproducible package includes the objective, domain and identifiers, data sources and versions, model and environment versions, configuration and parameter provenance, run command, logs, output inventory, checks, and known limitations.

## What to include with every design

- A settings table of the user's answers.
- The stages above, filled in for the user's case.
- A mermaid flowchart diagram that describes the various components and decisions made.
- **Limits to state with any result** (for example uncalibrated, unvalidated, short warm-up, unverified data coverage).
- **Open items:** skipped questions, unverified facts, and anything needing a model manual or domain expert.

Offer to save the design to a file. Do not write it without the user's confirmation.

## Model-specific references

Load a reference only after the user has chosen that model.

| Model | Reference |
| --- | --- |
| NOAA NextGen (NGEN) with the NGIAB data preprocessor | `references/ngen.md` |

If the user's model has no reference, rely on the general workflow, ask the user for the model's documentation, and do not invent commands.
