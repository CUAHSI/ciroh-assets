# NGEN reference

Load this only after the user has chosen to use the NextGen (NGEN) framework. It supplements the general workflow in `../SKILL.md`.

## Source and reliability

Everything here comes from the repository example `resources/NOAA NGEN - Preparing and Executing an NGEN Simulation/preparing-and-executing-ngen.ipynb` and its helper files (`notes.txt`, `prepare.sh`, `restore.sh`). The notebook's cells are unexecuted, so the commands are instructional, not verified results. It targets the CIROH 2i2c JupyterHub. It does not explain how to install NGEN or NGIAB on other platforms (for example a Mac). For those, point the user to the current NGIAB and NGEN documentation and do not invent steps.

The notebook example is a USGS-gaged basin in Alabama (site 02464000) for calendar year 2022. Treat site numbers, dates, and catchment IDs below as placeholders for the user's own.

## Supporting Files

The following files support this skill

| Topic | Location | Purpose |
| --- | --- | --- |
| Forcing | forcing/forcing.md | Additional information about meteorological forcing data. |
| Hydrofabric | hydrofabric/hydrofabric.md | Additional information about the NGEN Hydrofabric. |
| Models | models/models.md | Additional information about NextGen models. |
