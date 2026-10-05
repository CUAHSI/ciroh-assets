# CFE reference (Conceptual Functional Equivalent)

Load this only after the user has chosen a design that includes the CFE model, either standalone or as the runoff component in the NextGen (NGEN) framework. It supplements the general workflow in `../../SKILL.md` and the NGEN notes in `../ngen.md`.

## Source and reliability

This summary comes from four files in the public repository https://github.com/NOAA-OWP/cfe (default branch `master`), read on 2026-10-05: the landing page README, `MODEL.md`, `INSTALL.md`, and the BMI wrapper `src/bmi_cfe.c`. Commands below are copied in substance from `INSTALL.md`. They have not been run or verified here.

The Inputs, Outputs, and configuration details below were read from the code in `bmi_cfe.c`, so they describe what that file implements. Treat them as code-derived but not tested. Statements marked **(inference)** are interpretation, not something the code says.

Not read, so not covered: `src/cfe.c` (the core model, so its internal behavior is not verified), the configuration-parameter description linked from the README, the example files in `configs/`, the unit tests, and the GitHub issues. Check the repository's configuration documentation and example configs before finalizing parameter values.

## What CFE is

- A simplified conceptual rainfall-runoff model written by Fred Ogden. It is intended to be functionally equivalent to the National Water Model's runoff generation, vadose zone, and groundwater components, based on a "t-shirt" approximation of National Water Model versions 1.2, 2.0, and 2.1.
- Written in C and built with GCC and CMake. It implements the Basic Model Interface (BMI), so it can run standalone or be coupled to other BMI modules.

## Software License  
- Licensed under Apache-2.0.

## Formulation (from `MODEL.md`)

CFE replaces physically detailed National Water Model components with simple conceptual ones:

1. **Rainfall partitioning.** Rainfall is split into direct runoff and soil moisture with the Schaake function, a curve-number-like function of soil moisture deficit.
2. **Direct runoff timing.** Direct runoff reaches the catchment outlet through a geomorphological instantaneous unit hydrograph (GIUH). This removes the National Water Model's fine overland routing grid.
3. **Soil moisture storage.** Water going to soil moisture enters a conceptual linear reservoir with two outlets that share a minimum-storage activation threshold, which is tied to a field-capacity assumption. One outlet sends water to deep groundwater (scaled by the saturated hydraulic conductivity and a "slope" parameter where 1.0 means free drainage and 0.0 means no flow at the bottom). The other feeds lateral flow.
4. **Lateral flow timing.** Lateral flow is routed with a Nash cascade of reservoirs, producing a mass-conserving delayed response.
5. **Baseflow.** Groundwater contributes baseflow through an exponential nonlinear reservoir (as in the National Water Model) or a nonlinear reservoir that becomes linear when its exponent is 1.0.
6. **Evapotranspiration.** When coupled to a potential evapotranspiration (PET) module, actual ET is removed directly from precipitation and from soil using the Budyko function. A rootzone-based scheme is also available when CFE is coupled to the SoilMoistureProfiles module.

The GIUH and Nash cascade describe delays within a catchment. They are not channel routing. In the repo's NGEN notebook, channel routing is a separate component (T-Route).

## Inputs

Source: `INSTALL.md` for the run setup and `bmi_cfe.c` for the variables and configuration keys.

### Run setup

- **Configuration file** for CFE, plus a configuration file for each coupled module. When several are passed on the command line, the documented order is CFE, forcing, PET, then soil moisture profile.
- **Forcing.** In standalone mode CFE reads its own forcing file. It can instead receive precipitation through BMI from a forcing module, and the repository includes an AORC forcing reader for that.
- **PET** (optional) from a separate PET module, for the ET options above.
- **Soil moisture profile** (optional) from SoilMoistureProfiles, only for the rootzone-based ET example.

### BMI input variables

The BMI wrapper declares five input variables. All are `double`, on grid 0, at the node location.

| Variable | Units | Notes |
| --- | --- | --- |
| `atmosphere_water__liquid_equivalent_precipitation_rate` | mm h-1 | Precipitation. |
| `water_potential_evaporation_flux` | m s-1 | Potential ET, from a PET module. |
| `ice_fraction_schaake` | m | Commented in the code as coming from the Soil Freeze-thaw model. |
| `ice_fraction_xinanjiang` | none | Commented in the code as coming from the Soil Freeze-thaw model. |
| `soil_moisture_profile` | none (decimal fraction) | One value per soil layer. Used with the rootzone ET option. |

### Standalone forcing file

When `forcing_file` is anything other than `BMI`, CFE reads a text file. The first line is a header. Each later row is parsed as: year, month, day, hour, minute, second, precipitation (kg m-2), longwave, shortwave, pressure, specific humidity, air temperature, u wind, v wind. In the code I read, only time and precipitation are stored and used. Temperature is parsed but not used.

### Configuration file

The configuration is a `key=value` text file, with an optional `[units]` suffix on values.

**Always required:**

- `forcing_file` (a path, or `BMI`)
- Soil: `soil_params.depth`, `soil_params.bb`, `soil_params.satdk`, `soil_params.satpsi`, `soil_params.slop`, `soil_params.smcmax`, `soil_params.wltsmc`
- Groundwater and storage: `Cgw`, `expon`, `alpha_fc`, `soil_storage`, `gw_storage`, `max_gw_storage`
- Lateral flow: `K_nash_subsurface`, `K_lf`
- `surface_water_partitioning_scheme`: `Schaake` or `Xinanjiang`
- `num_timesteps`, unless `forcing_file` is `BMI`

**Required depending on the scheme chosen:**

- Surface runoff routing defaults to the GIUH, which requires `giuh_ordinates`.
- With the `NASH_CASCADE` surface runoff scheme instead, `N_nash_surface`, `K_nash_surface`, and `nash_storage_surface` are required.
- The Xinanjiang partitioning scheme requires `a_Xinanjiang_inflection_point_parameter`, `b_Xinanjiang_shape_parameter`, `x_Xinanjiang_shape_parameter`, and `urban_decimal_fraction`.

**Optional, with defaults in the code:**

| Key | Default |
| --- | --- |
| `refkdt` | 3.0 |
| `soil_params.expon`, `expon_secondary` | 1.0 |
| `N_nash_subsurface` | 2 |
| `nash_storage_subsurface` | all zeros |
| `verbosity` | 0 |
| `nsubsteps_nash_surface` | 10 |
| `Kinf_nash_surface` | 0.001 per hour |
| `retention_depth_nash_surface` | about 1 mm |

**Optional features:**

- `is_aet_rootzone=true` turns on the rootzone ET option and requires `soil_layer_depths` and `max_rootzone_layer`. The last layer depth must equal `soil_params.depth`, or initialization fails.
- `is_sft_coupled` with `ice_content_threshold` applies to the Schaake scheme only.

Most soil and groundwater parameters have **no default in the code**. They must be supplied, so a "baseline default parameter" run depends on whatever tooling generates them (for example the NGEN/NGIAB workflow), not on CFE itself.

### Parameters settable through BMI

These 18 can be changed at run time, which is how calibration tools can adjust them: `maxsmc`, `satdk`, `slope`, `b`, `Klf`, `Kn`, `Cgw`, `expon`, `max_gw_storage`, `satpsi`, `wltsmc`, `alpha_fc`, `refkdt`, `a_Xinanjiang_inflection_point_parameter`, `b_Xinanjiang_shape_parameter`, `x_Xinanjiang_shape_parameter`, `Kinf_nash_surface`, `retention_depth_nash_surface`.

### Time step

`time_step_size` defaults to 3600 s (1 hour). The code converts precipitation from mm h-1 to m using a 1-hour step (the code comment reads "mm/h to m w/ 1h timestep"). BMI time units are seconds, and fractional-step updates are supported. I did not verify behavior at time steps other than 1 hour. **(inference)** Treat hourly as the supported case until confirmed.

## Outputs

Source: `bmi_cfe.c`. The wrapper declares 15 output variables, all `double` with units of meters (m, a depth per time step) except the last one.

| Variable | Meaning / internal mapping |
| --- | --- |
| `RAIN_RATE` | Precipitation used by the model |
| `GIUH_RUNOFF` | Surface runoff routed by the GIUH. Same internal flux as `DIRECT_RUNOFF` |
| `DIRECT_RUNOFF` | Direct runoff. Same internal flux as `GIUH_RUNOFF` |
| `INFILTRATION_EXCESS` | Infiltration excess runoff |
| `NASH_LATERAL_RUNOFF` | Lateral flow from the subsurface Nash cascade |
| `DEEP_GW_TO_CHANNEL_FLUX` | Groundwater baseflow to the channel |
| `SOIL_TO_GW_FLUX` | Soil to groundwater flux (internal `flux_perc_m`) |
| `Q_OUT` | Total runoff leaving the catchment (internal `flux_Qout_m`) |
| `POTENTIAL_ET` | Potential ET |
| `ACTUAL_ET` | Actual ET |
| `GW_STORAGE` | Groundwater storage |
| `SOIL_STORAGE` | Soil storage |
| `SOIL_STORAGE_CHANGE` | Change in soil storage |
| `NWM_PONDED_DEPTH` | Water remaining in the GIUH or Nash surface queue, computed after each update |
| `SURF_RUNOFF_SCHEME` | Integer flag for the surface runoff scheme in use (units "none") |

Mass-balance terms (`NGEN_MASS_IN`, `NGEN_MASS_OUT`, `NGEN_MASS_STORED`, `NGEN_MASS_LEAKED`) are available through `get_value_ptr`.

**Depths, not discharge.** The runoff outputs are depths per time step. **(inference)** Getting discharge in m3 s-1 requires multiplying by catchment area and dividing by the time step length. Under NGEN this conversion and the channel routing are handled outside CFE, so confirm how the framework reports flow before comparing to gage data.

## Run modes in `INSTALL.md`

All five examples use data for CAMELS catchment 87 and a CMake switch.

| Mode | Switch | What it demonstrates |
| --- | --- | --- |
| Standalone | `-DBASE=ON` | CFE reads local forcing; no PET |
| Pseudo framework with forcing | `-DFORCING=ON` | AORC passes precipitation to CFE through BMI |
| Pseudo framework with PET | `-DFORCINGPET=ON` | Adds PET; ET via the Budyko function |
| Pseudo framework with rootzone ET | `-DAETROOTZONE=ON` | Adds SoilMoistureProfiles; ET from the deepest rootzone layer |
| NextGen framework | n/a | CFE coupled to PET inside NGEN |

Standalone build and run, per `INSTALL.md`:

```
git clone https://github.com/NOAA-OWP/cfe
cd cfe
git submodule update --init
mkdir build && cd build
cmake ../ -DBASE=ON
make && cd ..
./run_cfe.sh BASE
```

`INSTALL.md` recommends running the repository's unit tests before the examples. Build commands run inside `build/`; run commands run from the repository root.

For NGEN mode, `INSTALL.md` points to the NGEN tutorial and gives a build recipe that clones `ngen`, initializes submodules, and configures with `-DNGEN_WITH_BMI_C=ON`, `-DNGEN_WITH_BMI_FORTRAN=ON`, and `-DNGEN_WITH_EXTERN_ALL=ON`, which builds CFE, SLoTH, and PET. NGEN is then run with catchment and nexus data files plus a realization file. It notes that the library and config paths inside the realization file must match how the models were built. The NGEN notebook in this repo shows a higher-level route through the NGIAB tools.

## How to use this in a design

- **Scale.** CFE is a catchment-scale (lumped) model. Spatial structure comes from the framework around it, such as NGEN's catchment network, not from CFE.
- **Baseline run.** The code has no defaults for most soil and groundwater parameters, so "defaults" must come from other tooling. Under NGEN with the NGIAB tools, the repo's notebook says configuration is generated and some parameters are populated from basin attributes. Tell the user that parameter sources must be checked and recorded, and that defaults will not be calibrated.
- **Snow.** Neither the documentation nor `bmi_cfe.c` shows a snow or snowmelt routine (the forcing reader parses temperature but does not use it). For a snowmelt-driven basin, find out which component supplies snow and meltwater to CFE (in the NGEN notebook CFE is paired with Noah-OWP-Modular) and confirm that in the model documentation. Do not assume it.
- **ET.** Decide with the user whether to use PET only, or the rootzone option, and record the choice as a consequential assumption.
- **Time step.** The wrapper defaults to 3600 s and assumes a 1-hour step for the precipitation conversion. Design around hourly, and confirm anything else with the repository before proposing it.
- **Outputs.** CFE gives runoff depths. Plan the area conversion and routing explicitly, and decide which output (`Q_OUT`) is compared to observations.
- **Platform.** Building needs GCC and CMake. Whether the build works on a particular machine, such as an Apple silicon Mac, is not documented in what I read. Mark it as an open item.
