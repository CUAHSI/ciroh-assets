# NGEN reference

Load this only after the user has chosen to use the NextGen (NGEN) framework. It supplements the general workflow in `../SKILL.md`.

## Source and reliability

Everything here comes from the repository example `resources/NOAA NGEN - Preparing and Executing an NGEN Simulation/preparing-and-executing-ngen.ipynb` and its helper files (`notes.txt`, `prepare.sh`, `restore.sh`). The notebook's cells are unexecuted, so the commands are instructional, not verified results. It targets the CIROH 2i2c JupyterHub. It does not explain how to install NGEN or NGIAB on other platforms (for example a Mac). For those, point the user to the current NGIAB and NGEN documentation and do not invent steps.

The notebook example is a USGS-gaged basin in Alabama (site 02464000) for calendar year 2022. Treat site numbers, dates, and catchment IDs below as placeholders for the user's own.

## NGEN terms

These terms apply only to NGEN. Do not apply them to other models. Generic framework terms (Framework, BMI) are in [framework-glossary.md](framework-glossary.md), and model-agnostic terms are in [glossary.md](glossary.md).

| Term        | Definition                                                                                                                                                                                                                                                                                      | Avoid                                 |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| Nexus       | A point where catchment outflow is handed to the channel network or to another unit. **(inference)** The notebook does not define it.                                                                                                                                                           | "Junction", "node"                    |
| Hydrofabric | The dataset describing catchments and flowpaths, with their attributes and connectivity. The notebook **verifies** that it supplies geometry and model attributes and that its `divides` and `flowpaths` carry identifiers. That it also describes nexuses and connectivity is **(inference)**. | "Shapefile", "geometry" as a synonym  |
| Formulation | The ordered combination of process models applied to a catchment. **(inference)** The notebook does not define it. The CFE and Noah-OWP-Modular references use "Formulation" for a single model's own process options, which is a different meaning.                                            | "Model" for a combination; "pipeline" |
| Realization | The configuration that selects the models, forcings, routing, and time for a run. The notebook **verifies** that `realization.json` holds "model coupling and modular composition". That it also sets forcings, routing, and time is **(inference)**.                                           | "Config" without saying which file    |

## Supporting Files

The following files support this skill

| Topic       | Location                   | Purpose                                                                                                                            |
| ----------- | -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Terms       | framework-glossary.md      | Generic framework terms (Framework, BMI).                                                                                          |
| Forcing     | forcing/forcing.md         | Additional information about meteorological forcing data.                                                                          |
| Hydrofabric | hydrofabric/hydrofabric.md | Additional information about the NGEN Hydrofabric.                                                                                 |
| Models      | models/cfe.md              | Reference for the CFE (Conceptual Functional Equivalent) model.                                                                    |
| Models      | models/sloth.md            | Reference for SLoTH (Simple Logical Tautology Handler), a utility module that supplies constant or echoed values to other modules. |
| Models      | models/noah-owp.md         | Reference for Noah-OWP-Modular, a refactored Noah-MP land surface model (snow, energy balance, ET, soil water).                    |
