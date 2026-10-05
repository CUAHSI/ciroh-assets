# SLoTH reference (Simple Logical Tautology Handler)

Load this only after the user has chosen a design that uses a multi-module formulation in the NextGen (NGEN) framework, or another BMI framework, where one module needs an input that nothing else provides. It supplements the general workflow in `../../SKILL.md`, the NGEN notes in `../ngen.md`, and the CFE notes in `cfe.md`.

## Source and reliability

This summary comes from five files in the public repository https://github.com/NOAA-OWP/SLoTH (default branch `master`), read on 2026-10-05: the landing page README, `INSTALL.md`, `CHANGELOG.md`, `include/sloth.hpp`, and `src/sloth.cpp`. The Configuration and Outputs details below come from the README and the code. Commands are copied in substance from `INSTALL.md`. They have not been built or run here.

Statements marked **(inference)** are interpretation, not something the repository says.

Not read, so not covered: the unit tests in `test/`, the GitHub issues, and how NGEN itself consumes SLoTH (see the NGEN documentation for realization-file syntax).

## What SLoTH is

- A trivial BMI model. It does no hydrology. It reports whatever values you give it: a constant, or a copy of a value it received as input.
- Written in C++ against the `bmi-cxx` header, built with CMake into a shared library (`libslothmodel.so` on Linux, `libslothmodel.dylib` on macOS). It has no library dependencies. `bmi-cxx` and googletest come in as Git submodules.
- Its purpose is to fill gaps when several BMI modules are combined into one formulation. The README's example: a model needs a `soil_ice_fraction` input, but the study area never freezes, so SLoTH supplies a constant `0` instead of running a freeze-thaw model.
- Other uses named in the README: echoing a value (such as `temperature`) at the end of a timestep so it appears as a "previous timestep" input (`temperature_tminus1`) at the start of the next, and fixing values for integration tests or calibration experiments.

## Software License

- Licensed under Apache-2.0.

## Inputs

SLoTH has no required inputs and no meaningful physical inputs.

- **Configuration file.** None is read. `Initialize()` ignores its argument, and the README says to pass an empty string. Config-file support is listed as a wanted improvement.
- **Input variables.** Only those created through an *input alias* (see Configuration). With no aliases defined, SLoTH has zero input variables.
- **Forcing, parameters, time step.** None. SLoTH takes no forcing data and has no calibration parameters.

## Configuration

Variables are created by setting values on the model. Every variable and value set becomes a new **output** variable.

- **In NGEN (inference from the README):** the README says this suits NGEN's `model_params` mechanism: entries placed in `model_params` in the realization file are applied as if they were settings, and each becomes a SLoTH output variable with that value. Check the NGEN realization documentation for the exact syntax. The notebook in `resources/` shows SLoTH as the first of three modules in a realization file (SLoTH, Noah-OWP, CFE), and notes that module order matters.
- **In code:** `SetValue("name", &value)`.

### Variable metadata

A new variable gets these defaults unless you specify otherwise:

| Property | Default |
| --- | --- |
| Count | `1` |
| Type | `double` |
| Units | `1` (dimensionless) |
| Location | `node` |

Metadata can be given in parentheses after the name, in a fixed order: `name(count,type,units,location,input_alias)`.

| Example | Meaning |
| --- | --- |
| `somedoubles(3)` | 3 doubles, units `1` |
| `someints(4,int,cm)` | 4 ints, units `cm` |
| `someedgyfloats(3,float,m,edge)` | 3 floats, units `m`, location `edge` |
| `smellssweet(1,double,1,node,arose)` | Output `smellssweet` that echoes the last value received on input `arose` |

Rules from the README and code:

- Allowed types are `double`, `float`, `int`, `short`, and `long`. Any other type name raises an error.
- You cannot skip earlier fields to reach a later one in the README's description, but the code treats empty fields (for example `name(,,m)`) as "use the default". Prefer giving every field up to the one you need, as the README says.
- Units of `none`, `na`, `n/a`, `-`, `null`, `unitless`, or empty are reported as `1`.
- Metadata is needed only the first time a variable is set. Later sets use the bare name.
- Read values with the bare name. Reading with the metadata-decorated name is not supported.
- Changing metadata after creation is unsupported and may fail.
- The README says whitespace around the parameters is not allowed. The code I read trims whitespace, so the README may be out of date. **(inference)** Do not rely on whitespace being accepted.
- An alias cannot equal the variable's own name, and cannot match an existing output name. Several outputs may share one alias, and a received value is copied to all of them.
- **No conversion.** SLoTH never converts units or types. It only reports the metadata you gave it. Any conversion is up to the framework.

## Outputs

- The output variables are exactly the ones you defined. There is no fixed list.
- A value stays constant until you set it again, or until an aliased input arrives.
- Variable names, units, and counts must match what the downstream module expects. Look up the downstream model's input names in its own reference (for example the CFE inputs in `cfe.md`).

**(inference)** In the NGEN notebook's CFE formulation, SLoTH is the likely source of constant values for CFE inputs that no other module provides, such as the soil ice-fraction variables CFE declares. The notebook's text describes SLoTH only generally, so confirm in the generated `realization.json` which variables SLoTH actually supplies.

## Time behavior

- BMI time units are seconds. Start time is 0 and end time is the largest representable double.
- `GetTimeStep()` returns the most negative double, not a real step. Calling `Update()` repeatedly until a target time is reached would take effectively forever. The README expects frameworks to call `UpdateUntil(...)`, which simply sets the current time.
- SLoTH therefore does not constrain the simulation time step. The other modules do.

## How to use this in a design

- **Role.** SLoTH is plumbing, not a process model. Include it only when a downstream module needs an input that no chosen module produces. Do not list it as a hydrologic process.
- **Record every SLoTH value as an assumption.** A constant such as a zero ice fraction is a real modeling assumption (for example "no frozen ground"). For a snow-affected or cold-season basin, a zero ice fraction may be wrong. Flag it for the user to confirm.
- **Match names and units to the consumer.** SLoTH will happily publish a mismatched name or unit and the error will show up downstream.
- **Echo patterns.** Using SLoTH to carry values between timesteps is an advanced use. Mention it only if the user needs previous-timestep values.
- **Platform.** The build targets `.so` and `.dylib`, so macOS is a named target. Whether it builds on an Apple silicon Mac is not stated in what I read. Mark it as an open item.
