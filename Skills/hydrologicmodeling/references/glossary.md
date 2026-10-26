# Glossary of hydrology and modeling terms

Canonical vocabulary for the `hydrologicmodeling` skill. Use these terms when talking to the user, and check the user's wording against them. If the user uses an "avoid" term, confirm what they mean and then use the canonical term.

Scope: terms are model-agnostic and do not assume any model or modeling framework. Details of a single simulation (site numbers, dates, identifiers) belong in the design brief, never here.

## Spatial representation

| Term              | Definition                                                                                                            | Avoid                                                                    |
| ----------------- | --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| Domain            | The area, spatial units, and connections a simulation covers.                                                         | "Region", "area" when the model units matter                             |
| Basin (watershed) | The land area draining to a single outlet.                                                                            | "Catchment" for a whole basin; "drainage" alone                          |
| Catchment         | A single model spatial unit that drains to one outlet point. A basin is made of one or more catchments.               | "Basin", "subbasin", "polygon" as synonyms                               |
| Outlet            | The point where a basin or catchment drains.                                                                          | "Pour point", "mouth" without saying which                               |
| Gage              | A station that measures a quantity (usually streamflow) at a location. A gage is a location anchor, not an objective. | "Gauge" (spelling varies; pick one), "station" for a non-measuring point |
| Reach             | A segment of river channel between two connection points.                                                             | "Stream", "river" for a single segment                                   |
| Spatial resolution | The size of the spatial unit the model resolves, such as grid cell size or typical catchment area. State the units.   | "Scale", "detail" without a size                                         |
| Spatial discretization (lumped or gridded) | How the domain is divided. Lumped: one unit represents the whole basin. Gridded (distributed): a regular grid of cells, each simulated separately. | "Distributed" alone, since it can also mean catchment-based units |

## Inputs and states

| Term               | Definition                                                                                                                      | Avoid                                                     |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| Forcing            | Time-varying meteorological input that drives the model, such as precipitation and temperature.                                 | "Weather data", "input data" when forcing is meant        |
| Boundary condition | A value imposed at the edge of the domain, such as inflow from upstream.                                                        | "Forcing" for static or spatial-edge values               |
| Static attribute   | A time-invariant property of a unit, such as area, slope, or soil class.                                                        | "Forcing", "constant"                                     |
| Parameter          | A value that controls how a process behaves. It may be calibrated, defaulted, or derived from attributes.                       | "Setting", "variable"                                     |
| State              | A stored quantity that evolves during the run, such as soil moisture.                                                           | "Parameter", "variable"                                   |
| Initial condition  | The state values at the start of the simulation.                                                                                | "Starting data", "defaults"                               |
| Spin-up (warm-up)  | A period run before the evaluation period so states can adjust away from the initial conditions. Results from it are discarded. | "Burn-in", "equilibration" without saying it is discarded |

## Model structure

| Term                   | Definition                                                                                   | Avoid                                                                 |
| ---------------------- | -------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Model                  | Software implementing one or more process representations.                                   | "Simulation" for the software                                         |
| Process model (module) | One component representing a process, such as snow, soil water, or routing.                  | "Submodel", "plugin"                                                  |
| Configuration          | The files and settings that define a run: domain, components, parameters, paths, and period. | "Setup" as a noun for files                                           |
| Routing                | Movement of water through the channel network between catchments.                            | "Flow" or "delay"; do not confuse with within-catchment runoff timing |

## Time

| Term              | Definition                                                                                                 | Avoid                                        |
| ----------------- | ---------------------------------------------------------------------------------------------------------- | -------------------------------------------- |
| Simulation period | Start and end times of the run, including warm-up.                                                         | "Dates" without saying which                 |
| Evaluation period | The part of the simulation period used to judge results.                                                   | "Run period"                                 |
| Time step (temporal resolution)         | The interval at which the model advances.                                                                  | "Resolution" for time alone                  |
| Time zone         | The time convention of timestamps. Always state it; convert, do not strip it.                              | "Local time" without a zone                  |
| Water year        | A 12-month period for hydrologic accounting. Its start month depends on the convention and must be stated. | "Last year", "last water year" without dates |

## Evaluation

| Term                                 | Definition                                                                                      | Avoid                                    |
| ------------------------------------ | ----------------------------------------------------------------------------------------------- | ---------------------------------------- |
| Observation                          | Measured data used as reference for the result.                                                 | "Truth", "ground truth"                  |
| Baseline run                         | A run with default or derived parameters and no calibration.                                    | "Test run"                               |
| Test run                             | A short run used only to confirm the setup executes.                                            | "Baseline run"                           |
| Calibration                          | Adjusting parameters to improve agreement with observations. Requires observations.             | "Tuning", "fitting" without observations |
| Validation                           | Checking results against observations not used in calibration.                                  | "Verification" as a synonym              |
| Verification (of inputs and outputs) | Checks that data are structurally and physically plausible.                                     | "Validation"                             |
| Unit conversion                      | Changing quantity units (for example depth per time step to discharge). State the factors used. | "Scaling"                                |
