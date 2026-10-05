# Noah-OWP-Modular reference (Noah-OWP-Modular land surface model)

Load this only after the user has chosen a design that includes Noah-OWP-Modular, either standalone or as a module in the NextGen (NGEN) framework. It supplements the general workflow in `../../SKILL.md`, the NGEN notes in `../ngen.md`, and the CFE notes in `cfe.md`, which it is commonly paired with.

## Source and reliability

This summary comes from the public repository https://github.com/NOAA-OWP/noah-owp-modular (default branch `main`), read on 2026-10-05. Files read: the landing page README, `INSTALL.md`, `docs/changelog.md`, the BMI wrapper `bmi/bmi_noahowp.f90`, `src/NamelistRead.f90`, `src/OptionsType.f90`, and the example `run/namelist.input`. I also read the directory listings for `src/`, `bmi/`, `parameters/`, `run/`, and `docs/`.

The Inputs, Outputs, and configuration details below were read from that code, so they describe what those files implement. Statements marked **(inference)** are interpretation, not something the repository says.

Not read, so not covered: the process modules in `src/` other than the two above (so the descriptions under Formulation come from file names and option comments, not from the equations), the parameter tables in `parameters/`, the forcing file format used by the standalone driver, the tests, the wiki, and the GitHub issues.

## What Noah-OWP-Modular is

- An extended, refactored version of the Noah-MP land surface model, adapted from the single-file source code at https://github.com/NCAR/noahmp/. It is split into modules and data types for readability and interoperability. It currently excludes crop and carbon components.
- Written in Fortran and built with CMake. It implements the Basic Model Interface (BMI), and it builds either as a standalone driver or as a BMI shared library. A compiler directive (`NGEN_ACTIVE`) builds the NGEN-compatible interface.
- The README adds a subsurface option so the model can run with the original Noah-MP subsurface or with alternative subsurface treatments.
- The README describes the model as in active development.

## Software License

- Licensed under Apache-2.0.

## Formulation (from source file names and `OptionsType.f90`)

The README does not list the processes. This list is taken from the source file names and the option descriptions in `OptionsType.f90`. **(inference)** It is a map of what the code is organized around, not a verified description of the equations.

1. **Atmospheric processing and precipitation phase.** Precipitation is split into rain and snow using one of seven options: the Jordan (1991) SNTHERM equation, fixed air-temperature thresholds (2.2 °C or 0 °C), the phase supplied by a weather model, a user-defined air-temperature threshold, a user-defined wet-bulb threshold, or a logistic regression model (Jennings et al., 2018).
2. **Canopy interception.** Canopy water and interception modules (`CanopyWaterModule`, `InterceptionModule`).
3. **Snow.** Snow layering, snow water, snow albedo (BATS or CLASS option), and snow/soil temperature modules.
4. **Radiation and energy balance.** Shortwave radiation (two-stream options), albedo, thermal properties, surface drag, and an energy module.
5. **Evapotranspiration.** An ET flux module, with options for stomatal resistance (Ball-Berry or Jarvis for canopy; Noah, CLM, SSiB, or a maximum-transpiration option for the soil moisture factor) and surface resistance to evaporation.
6. **Soil water and runoff.** Surface runoff and infiltration, soil water movement, and subsurface runoff. Runoff options include TOPMODEL variants, free drainage, BATS, VIC, Xinanjiang, and dynamic VIC (with Philip, Green-Ampt, or Smith-Parlange infiltration).
7. **Subsurface realization.** Full Noah-MP style subsurface, or one-way coupled hydrostatic. The two-way coupled option is marked "NOT IMPLEMENTED YET" in the code.
8. **Vegetation.** Dynamic vegetation options (table LAI or input LAI, with different vegetation fraction treatments). Crop models are not supported.

## Inputs

Source: `bmi_noahowp.f90`, `NamelistRead.f90`, `OptionsType.f90`, the example `namelist.input`, and `INSTALL.md`.

### BMI input variables

The BMI wrapper declares eight input variables, all single-precision real, on grid 0 (scalar):

| Variable | Units | Meaning (from the code comments) |
| --- | --- | --- |
| `SFCPRS` | Pa | Surface pressure |
| `SFCTMP` | K | Surface air temperature |
| `SOLDN` | W m-2 | Incoming shortwave radiation |
| `LWDN` | W m-2 | Incoming longwave radiation |
| `UU` | m s-1 | Wind speed, eastward |
| `VV` | m s-1 | Wind speed, northward |
| `Q2` | kg kg-1 | Mixing ratio |
| `PRCPNONC` | mm s-1 | Precipitation rate |

Noah-OWP-Modular needs a full set of meteorological forcing (radiation, pressure, humidity, wind, temperature, and precipitation), not just precipitation and temperature. Compare this with CFE, which only uses precipitation (see `cfe.md`).

### Configuration file (namelist)

Configuration is a Fortran namelist file (the example is `run/namelist.input`). The reader stops with an error if any entry is missing, so **every key below is required and none has a default in the code**. The values shown are from the shipped example (a point run at Bondville, Illinois). They are example values, not recommendations.

| Group | Keys | Example |
| --- | --- | --- |
| `timing` | `dt`, `startdate`, `enddate`, `forcing_filename`, `output_filename` | `dt` = 1800 s; dates as `YYYYMMDDhhmm` in UTC |
| `parameters` | `parameter_dir`, `general_table`, `soil_table`, `noahowp_table`, `soil_class_name`, `veg_class_name` | `GENPARM.TBL`, `SOILPARM.TBL`, `MPTABLE.TBL`; soil class `STAS` or `STAS-RUC`; vegetation class `MODIFIED_IGBP_MODIS_NOAH` or `USGS` |
| `location` | `lat`, `lon`, `terrain_slope`, `azimuth` | degrees; slope and azimuth in degrees |
| `forcing` | `ZREF`, `rain_snow_thresh` | wind measurement height (m); rain-snow threshold (°C) |
| `model_options` | 17 option switches (below) | see example values below |
| `structure` | `isltyp`, `nsoil`, `nsnow`, `nveg`, `vegtyp`, `croptype`, `sfctyp`, `soilcolor` | soil texture class, layer counts, vegetation type, surface type (1 soil, 2 lake) |
| `initial_values` | `dzsnso`, `sice`, `sh2o`, `zwt` | layer thicknesses (m), initial soil ice and liquid (m3 m-3), initial water table depth (m) |

`dzsnso` holds one value per snow layer followed by one per soil layer (`nsnow` + `nsoil` values). `sice` and `sh2o` hold one value per soil layer. The code derives the layer-bottom depths (`zsoil`) and total soil depth from `dzsnso`.

**Model options.** The code checks each option against the range shown. Meanings are from `OptionsType.f90`.

| Key | Valid values | What it selects |
| --- | --- | --- |
| `precip_phase_option` | 1-7 | Rain/snow partitioning (see Formulation) |
| `runoff_option` | 1-8 | Runoff scheme (1 TOPMODEL with groundwater, 2 TOPMODEL with equilibrium water table, 3 free drainage, 4 BATS, 5 Miguez-Macho and Fan, 6 VIC, 7 Xinanjiang, 8 dynamic VIC) |
| `drainage_option` | 1-8 | Drainage from the soil column; options 4 and 6-8 reuse values from the matching runoff option |
| `frozen_soil_option` | 1-2 | Frozen soil permeability |
| `dynamic_vic_option` | 1-3 | Infiltration in dynamic VIC (Philip, Green-Ampt, Smith-Parlange) |
| `dynamic_veg_option` | 1-9 | Dynamic vegetation and LAI/vegetation-fraction source |
| `snow_albedo_option` | 1-2 | BATS or CLASS |
| `radiative_transfer_option` | 1-3 | Two-stream variants |
| `sfc_drag_coeff_option` | 1-2 | Monin-Obukhov or original Noah |
| `canopy_stom_resist_option` | 1-2 | Ball-Berry or Jarvis |
| `crop_model_option` | 0 | No crop model supported |
| `snowsoil_temp_time_option` | 1-3 | Snow/soil temperature time scheme |
| `soil_temp_boundary_option` | 1-2 | Soil temperature lower boundary |
| `supercooled_water_option` | 1-2 | Supercooled liquid water |
| `stomatal_resistance_option` | 1-4 | Soil moisture factor for stomatal resistance; 4 maximizes transpiration to approximate potential ET |
| `evap_srfc_resistance_option` | 1-5 | Surface resistance to evaporation; 5 minimizes it to approximate potential ET |
| `subsurface_option` | 1-3 | 1 full Noah-MP style, 2 one-way coupled hydrostatic, 3 not implemented |

The changelog says options `stomatal_resistance_option = 4` and `evap_srfc_resistance_option = 5` were added to approximate PET, with the output still named `EVAPOTRANS`.

The example uses `precip_phase_option` 1, `runoff_option` 8, `drainage_option` 8, `dynamic_veg_option` 1, `snowsoil_temp_time_option` 3, `soil_temp_boundary_option` 2, `subsurface_option` 1, and 1 for most of the remaining switches.

### Parameters

Soil and vegetation parameters come from three table files in the repository's `parameters/` directory: `GENPARM.TBL`, `SOILPARM.TBL`, and `MPTABLE.TBL`. Which row is used depends on `isltyp`, `vegtyp`, and the class names. I did not read the tables, so I cannot state specific values.

The BMI wrapper exposes a set of parameters for run-time reads and writes, which is how calibration tools can adjust them: `AXAJ`, `BXAJ`, `XXAJ`, `BEXP`, `DKSAT`, `SMCMAX`, `CWP`, `FRZX`, `HVT`, `KDT`, `REFKDT`, `MFSNO`, `MP`, `RSURF_EXP`, `RSURF_SNOW`, `SCAMAX`, `SLOPE`, and `VCMX25`. Setting `DKSAT` or `REFKDT` also recomputes `KDT`, and setting `SMCMAX` also recomputes `FRZX`. Units found in the code: `DKSAT` m s-1, `SMCMAX` volumetric, `CWP` 1/m, `HVT` m, `RSURF_SNOW` s m-1, `VCMX25` umol CO2 m-2 s-1, and the rest unitless. What each parameter means was not read, except where the name matches a documented option. `BEXP`, `DKSAT`, and `SMCMAX` are vectors with one value per soil layer.

### Time step

The time step `dt` is set in the namelist (1800 s in the example), and BMI time is in seconds. The end time is the number of steps times `dt`. `update_until` advances only whole time steps; fractional steps are commented out as not implemented. I did not verify which time steps the model supports. **(inference)** Choose `dt` to match the other modules in the formulation, and confirm.

## Outputs

Source: `bmi_noahowp.f90`. The wrapper declares 23 output variables. All are single-precision real except `ISNOW`, which is an integer.

| Variable | Units | Meaning (from the code comments) |
| --- | --- | --- |
| `QINSUR` | m s-1 | Total liquid water input to the surface |
| `ETRAN` | mm | Transpiration. Returned as rate times `dt`, so a depth per time step |
| `QSEVA` | mm s-1 | Evaporation rate |
| `EVAPOTRANS` | m s-1 | Evapotranspiration rate |
| `TG` | K | Surface/ground temperature (snow surface temperature when snow is present) |
| `SNEQV` | mm | Snow water equivalent |
| `TGS` | K | Ground temperature (equals `TG` with no snow, bottom snow element temperature with snow) |
| `ACSNOM` | mm | Accumulated meltwater from the bottom snow layer |
| `SNOWT_AVG` | K | Average snow temperature (by layer mass) |
| `ISNOW` | unitless | Number of snow layers |
| `QRAIN` | mm s-1 | Rainfall rate on the ground |
| `FSNO` | unitless | Snow-cover fraction on the ground |
| `SNOWH` | m | Snow depth |
| `SNLIQ` | mm | Snow layer liquid water (one value per snow layer) |
| `QSNOW` | mm s-1 | Snowfall rate on the ground |
| `ECAN` | mm | Evaporation of intercepted water. Rate times `dt`, so a depth per time step |
| `GH` | W m-2 | Heat flux into the soil |
| `TRAD` | K | Surface radiative temperature |
| `FSA` | W m-2 | Total absorbed shortwave radiation |
| `CMC` | mm | Total canopy water (liquid plus ice) |
| `LH` | W m-2 | Total latent heat to the atmosphere |
| `FIRA` | W m-2 | Total net longwave radiation to the atmosphere |
| `FSH` | W m-2 | Total sensible heat to the atmosphere |

Variables 8 onward (`ACSNOM` through `FSH`) are labeled in the code as NWM 3.0 output variables.

**Mixed units.** The outputs mix rates (m s-1, mm s-1) and per-time-step depths (mm). Check units before combining or comparing them. The code comment calls `QINSUR` the total liquid water input to the surface, while the NGEN notebook in `resources/` describes it as the surface runoff that Noah-OWP passes to CFE. Treat the meaning of `QINSUR` as something to confirm in the realization file, not assume.

**Standalone output.** The standalone driver writes a NetCDF file to the path in `output_filename` (`data/output.nc` in the example per `INSTALL.md`).

## How to use this in a design

- **Scale.** Noah-OWP-Modular runs at a single point. Its namelist holds one latitude and longitude, one soil type, and one vegetation type, and the changelog says the model is set up to run at a single point. For a catchment, the framework runs one instance per catchment with its own configuration. **(inference)** Confirm how that configuration is generated and which catchment attributes it uses.
- **Role next to CFE.** CFE has no snow routine (see `cfe.md`). Noah-OWP-Modular does snow, energy balance, and ET, and the NGEN notebook runs it ahead of CFE. **(inference)** In that pairing Noah-OWP-Modular is the likely snow and ET component, so a snowmelt-driven basin depends on it. Confirm the variable mapping between the two modules in the realization file.
- **Overlapping soil processes.** Noah-OWP-Modular has its own soil moisture, runoff, and drainage options (`runoff_option`, `drainage_option`, `subsurface_option`), and CFE has its own soil and groundwater storage. Ask which options are used in the user's configuration and record the choice as a consequential assumption, so the same process is not silently counted twice.
- **Forcing.** The design must include a forcing source that provides all eight inputs with matching units: pressure, temperature, shortwave, longwave, both wind components, humidity, and precipitation. Confirm that the chosen source covers all of them for the simulation period.
- **Baseline parameters.** The code has no defaults for configuration keys, and parameters come from the three table files by soil and vegetation class. A baseline run therefore depends on the soil and vegetation classes chosen for the catchment. Record them, and note that table values are not calibrated.
- **Initial conditions.** Initial soil liquid and ice, and the water table depth, are set in the namelist. Plan a spin-up period so the results do not depend on those starting values.
- **ET and PET.** If a design needs potential ET from this model, options 4 (`stomatal_resistance_option`) and 5 (`evap_srfc_resistance_option`) approximate it, with the output still named `EVAPOTRANS`. Decide with the user whether to use them, and record it as an assumption.
- **Time step.** `dt` is a user setting. Match it to the other modules, and confirm that the chosen value is supported.
- **Units.** Convert deliberately between the rate outputs and the per-time-step depth outputs.
- **Platform.** The README says the model has been tested on Unix-based systems such as macOS and Linux, and it depends on NetCDF with Fortran bindings. Whether it works on a particular machine, such as an Apple silicon Mac, is not documented in what I read.
