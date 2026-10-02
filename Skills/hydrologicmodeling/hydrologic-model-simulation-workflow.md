# Hydrologic Model Simulation Workflow: Skill Seed

## Purpose and scope

This note distills the repository's NOAA NextGen (NGEN) tutorial into a general workflow for creating a hydrologic model simulation. It is intended as source material for a future agent skill, not as a model-specific command reference. The workflow applies across model families, but the selected model's documentation and data conventions always control.

The concrete example comes from `resources/NOAA NGEN - Preparing and Executing an NGEN Simulation/preparing-and-executing-ngen.ipynb`. That notebook describes a USGS-gaged basin in Alabama, a NextGen hydrofabric subset, meteorological forcing preparation, component configuration, NGEN execution, and comparison of routed flow with USGS observations. The notebook cells are unexecuted, so its described outputs and commands are instructional rather than verified run results.

## What a simulation needs

A useful mental model is that a simulation joins five things:

1. **A question and domain**: what process, location, period, and decision or scientific question are in scope?
2. **A spatial and temporal representation**: what are the model units (basins, catchments, grid cells, reaches), how are they connected, and at what time step will the simulation run?
3. **Inputs and parameters**: meteorological or boundary forcings, static physical attributes, initial states, and parameter values.
4. **A model formulation and executable environment**: the chosen process models, routing or coupling choices, configuration files, software versions, and runtime dependencies.
5. **A verification and evaluation strategy**: checks that inputs and outputs are structurally and physically plausible, plus observations or other evidence for evaluating the intended question.

The names and file formats differ by model. A hydrofabric and routed network are central to the NGEN example, but not every hydrologic model uses either one.

## End-to-end workflow

### 1. Define the modeling objective

Before acquiring data, write down:

- The process and question to be modeled (for example, basin runoff, streamflow, soil-water response, or flood routing).
- The study area and outlet or other locations of interest.
- The simulation period, warm-up period, time zone, and intended computational time step.
- The spatial and temporal resolution needed to answer the question.
- The output variables and evaluation evidence that will indicate a useful result.

**Checkpoint:** The objective can be stated in a way that determines the domain, inputs, model configuration, and evaluation method. A gage is a useful location anchor, but it does not by itself define the whole modeling objective.

### 2. Select the model and assemble the domain

Choose a model whose supported processes, spatial representation, scale, data needs, and computational requirements fit the objective. Identify the domain representation the model expects: for example, basin polygons, a gridded mesh, a catchment network, or a river network.

Acquire or build the required spatial data and derive the model domain. If starting from a point such as a stream gage, determine which upstream units and network features belong in the simulation. Preserve stable identifiers and the relationships among spatial units, channels, outlets, and observations. Inspect the domain on a map and inspect its attributes; confirm coordinate reference systems, topology, units, and expected feature counts.

**NGEN example:** The notebook uses a USGS site number to ask NGIAB to subset the NextGen hydrofabric. It then inspects the GeoPackage's divides (catchments), flowpaths (reaches), nexus features (network connections), and associated attributes. It also uses an outlet catchment identifier later to locate the corresponding routed feature.

**Checkpoint:** The domain covers the intended contributing area, has connected and correctly identified features, and includes the attributes needed by the selected model and routing scheme.

### 3. Acquire and prepare forcing and boundary data

Choose data sources that cover the entire domain and simulation period at suitable spatial and temporal resolution. Determine required variables, units, calendar/time convention, projection, missing-data policy, and any interpolation or spatial mapping method. Prepare data in the model's required format and map each spatial unit to the appropriate input data.

Inspect the prepared data before running the model:

- Confirm variable names and units against model requirements.
- Check dimensions, spatial identifiers, timestamps, time step, coverage, and missing values.
- Plot representative variables for one or more model units and inspect plausible ranges and event timing.
- Record the forcing source, version, transformations, and spatial extraction or interpolation method.

**NGEN example:** NGIAB is used to create forcings for a selected site and date interval; the tutorial previews a NetCDF file with xarray, checks variables and dimensions, and plots precipitation and temperature at an outlet catchment. It refers to “exact extract,” but does not explain that method in enough detail to prescribe it generally. Choose and document the extraction method appropriate to the forcing grid and model.

**Checkpoint:** Every required model unit and time step has valid forcing values with understood units and no unexplained temporal or spatial gaps.

### 4. Configure model components, parameters, and time controls

Translate the conceptual model into the chosen software's configuration. Specify the process components and their order or coupling, spatial parameter files, initial states, routing method if applicable, forcing paths, simulation start and end, output interval, and requested outputs. Use defensible parameter sources and record assumptions. Distinguish calibrated parameters from defaults or values inherited from geospatial attributes.

Review generated files rather than treating configuration generation as proof of correctness. Check paths, identifiers, time coverage, units, supported options, and consistency between component settings. Run the software's configuration validation or smallest available test where supported.

**NGEN example:** The notebook uses NGIAB to produce per-catchment CFE and Noah-OWP-M inputs, a `realization.json` describing model component composition and coupling, and a `troute.yaml` for channel routing. The realization also points to forcing data and time controls. The example emphasizes that module order matters and that spatial attributes populate some model parameters.

**Checkpoint:** Configuration references the intended domain and forcings; all components agree on identifiers, units, time period, and exchange variables; parameters have traceable sources.

### 5. Prepare a reproducible runtime and execute

Use a clean, documented environment with the required executable, model components, libraries, and data-access tools. Check software versions and the current model command-line interface; command syntax and configuration schemas may change. Keep large inputs and outputs organized, and avoid replacing shared or global datasets when a local project copy or isolated environment will work.

Run a small test or short period first when practical. Capture the exact command, environment, logs, exit status, configuration, and input versions. Then run the full simulation and confirm that it completed without errors and produced the expected outputs.

**NGEN example:** The notebook changes into the site-specific working directory and invokes the `ngen` executable with the subset GeoPackage and realization configuration. It recommends using a prepared runtime environment where needed. Its helper scripts temporarily replace shared NGIAB hydrofabric files, so that operational pattern should not be copied casually: isolate such changes, make backups, and verify restoration.

**Checkpoint:** The process exits successfully, logs show the intended run period and domain, and expected output files exist and are non-empty. A command returning without an obvious error is not sufficient scientific validation.

### 6. Verify and evaluate outputs

First verify outputs internally, before comparing them with observations:

- Check file structure, variables, dimensions, identifiers, timestamps, units, missing values, and expected time coverage.
- Plot key states and fluxes and inspect for impossible values, discontinuities, drift, or timing offsets.
- Check water-balance or other conservation relationships when supported by the formulation.
- Confirm that outputs correspond to the intended spatial feature; trace identifiers between domain, model output, and observation site.

Then evaluate against independent observations or other references relevant to the objective. Align time zones, units, sampling intervals, and timestamps before comparing. Plot hydrographs and examine event timing, volume, peak magnitude, and low-flow behavior. Report appropriate metrics and their limitations. Consider flow-duration or bias-duration analyses when regime-dependent behavior matters. Do not treat a visual match or a single metric as proof of model validity.

**NGEN example:** The notebook reads T-Route output, maps an outlet catchment identifier to a routing feature identifier, plots modeled flow, retrieves USGS observations, converts units, aligns the series to an hourly index, and sketches a bias-duration curve. The tutorial's example unit conversion and bias calculation should be independently checked for the actual observation units, timestamps, missing values, and zero flows before reuse.

**Checkpoint:** Evaluation compares the same location, quantity, units, and times, and interpretation distinguishes input/configuration problems from model structural or parameter problems.

### 7. Iterate, document, and package

Use verification and evaluation results to identify the next change. Change one class of assumptions at a time where possible (for example, forcing, parameters, initial conditions, or routing) so effects are interpretable. Preserve each run's configuration and results rather than overwriting the only copy. Record known limitations, failed checks, and decisions alongside successful runs.

A reproducible simulation package should include:

- Objective, domain definition, and identifiers.
- Input data sources, versions, licenses where relevant, preprocessing, units, and time conventions.
- Model and component versions, environment or build details, and run command.
- Configuration and parameter files, including initial conditions and parameter provenance.
- Logs, output inventory, verification checks, evaluation methods and results.
- Known limitations and instructions to reproduce the run.

## Adaptable checklist for a future skill

Before execution, the skill should help a user answer:

- What question is this simulation meant to answer, and what are its spatial and temporal boundaries?
- Which model and spatial representation fit that question, and why?
- What domain, forcings, boundary conditions, observations, and parameters are required?
- Have the data been checked for identifiers, coverage, units, coordinate systems, time zones, missing values, and plausible ranges?
- How are model components coupled, and which configuration files control time, parameters, forcing, routing, and outputs?
- Can a short test expose configuration or runtime issues before the full run?
- What checks will establish that the run completed and that outputs are internally plausible?
- What observations or independent evidence will evaluate performance, and how will units and timestamps be aligned?
- Can another person reproduce the run from the recorded files, versions, commands, and decisions?

The skill should ask for missing context instead of inventing model-specific commands, defaults, parameter values, or data conventions. It should separate universal workflow guidance from model-specific instructions and tell the user when a domain expert or model manual is needed.

## Source mapping

The source notebook's major sections map to the general workflow as follows:

| Source notebook activity | General workflow stage |
| --- | --- |
| Select USGS gage; subset and inspect HydroFabric | Define objective; assemble and validate domain |
| Collect forcings; inspect NetCDF variables and time series | Acquire, prepare, and quality-check inputs |
| Generate and inspect component, realization, and routing files | Configure model and parameters |
| Run NGEN using domain and realization inputs | Prepare runtime and execute |
| Inspect routed output; compare with USGS observations | Verify and evaluate |
