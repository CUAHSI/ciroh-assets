# Example: Logan River streamflow intake (test run)

This is a condensed record of a test of the `hydrologicmodeling` skill. It shows the intended interview style: one question at a time, no plan until intake is complete, and decisions left to the user.

**This run stopped before a design was produced.** The user ended the session during intake, so the example does not include the stage-by-stage design or the flowchart the skill would normally produce. Nothing from the run was saved to the repository except this write-up.

Dates are relative to the test day, October 5, 2026. Assistant text is condensed; tables are kept because they carry the useful content.

## Starting request

> I want to create a model simulation for the Logan River watershed to predict river streamflow. The simulation period is the prior water year and the model should have a 6-month spin up time. I would like to use commonly available models.

## Transcript

### 1. Restating what was given, then the time step

The assistant listed what it had taken from the request and asked the user to correct it:

- Objective: predict streamflow.
- Study area: the Logan River watershed, assumed to be the Logan River in northern Utah, with the outlet still to be pinned down.
- Period: "the prior water year" interpreted as water year 2026 (October 1, 2025 to September 30, 2026).
- Warm-up: 6 months (April 1, 2025 to September 30, 2025), excluded from evaluation.
- Model: deferred, to be revisited at the end.

**Question:** What time step do you want, and what time zone are your data in?

**User:** Hourly.

### 2. Evaluation evidence

The assistant marked the time zone as an open item and asked how the user would judge the simulation.

**Question:** Do you have observations to compare against, or would you use internal plausibility checks only?

**User:** There should be a USGS gauge on the Logan River near the entrance to Logan Canyon.

### 3. Confirming the gage

The assistant did not guess a site number. It looked the gage up in the USGS site service (read-only metadata, not model data) and reported:

| Field | Value |
| --- | --- |
| Site number | 10109000 |
| Name | Logan River above State Dam, near Logan, UT |
| Location | 41.74355° N, 111.78398° W (NAD83) |
| Drainage area | 214 (USGS reports square miles) |
| HUC | 16010203 |
| Time zone | MST, observes daylight saving time |

It noted that the site record does not say "canyon mouth", so that part was an inference, and that observation timestamps would need aligning with the model.

**Question:** Is USGS 10109000 the gage you meant?

**User:** Yes, that's correct.

### 4. Existing materials

**Question:** Do you already have a domain, forcing data, parameters, or configuration files, or are you starting from scratch?

**User:** I haven't collected any data yet. Can you list what I'll need and recommend sources for the data?

The assistant answered at a model-agnostic level, since the model was not yet chosen, and flagged that it had not verified current access or coverage dates:

| What you need | Why | Recommended sources |
| --- | --- | --- |
| Watershed boundary upstream of the gage | Defines the model domain | USGS StreamStats, or delineate from the gage location using a DEM |
| Terrain (DEM) | Elevation, slope, flow directions | USGS 3DEP |
| Stream network | Channel routing, if the model uses it | USGS NHDPlus (High Resolution) |
| Land cover | Vegetation and runoff parameters | NLCD |
| Soils | Infiltration and storage parameters | USDA NRCS gSSURGO, or POLARIS |
| Hourly meteorological forcing | Drives the simulation | NOAA AORC, NLDAS-2, or ERA5-Land |
| Observed streamflow at 10109000 | Evaluation reference | USGS NWIS |
| Snow observations (optional) | Check snow behavior | NRCS SNOTEL, NOAA SNODAS |

Caveats it raised: forcing coverage near the present is unverified because the run window ends days before the test; recent USGS data are provisional; the gage reports in Mountain time while forcing products are commonly UTC; and gridded products can smooth mountain precipitation.

**Question:** Baseline run with default parameters, or calibration against the gage?

**User:** I want to use baseline default parameters. What model(s) should I use?

### 5. Model comparison

The skill treats the model choice as optional and last, but the user asked directly, so the assistant compared options against the stated requirements (hourly, no calibration) and said the assessment was its own and unverified.

Key reasoning: a baseline with default parameters works best with models that derive parameters from basin attributes, because many conceptual models have parameters that are only meaningful once calibrated.

| Model | Hourly | Defaults without calibration | Notes |
| --- | --- | --- | --- |
| NGEN (via NGIAB) | Yes | Yes, configuration is generated from basin attributes | Has a worked example in this repository; that example targets a hosted JupyterHub |
| SUMMA + mizuRoute | Yes | Partly | More flexible, more decisions to document |
| VIC | Sub-daily possible | Needs parameter files | Usually run at coarser scales |
| HEC-HMS | Yes | Weak | Most parameters are estimated by hand |
| WRF-Hydro | Yes | Possible | Likely overkill for one basin |
| SWAT, PRMS | Typically daily | n/a | Do not match the hourly requirement |

**Question:** Where will you run the simulation?

**User:** On a laptop.

### 6. Platform detail

**Question:** Which operating system, and if a Mac, Apple silicon or Intel?

**User:** Mac, M1.

### 7. Verification attempt, then the last open item

The assistant tried to check the NGIAB project documentation for Apple silicon support. The user declined the lookup. The assistant did not retry and did not guess: it marked Apple silicon support as unverified, to be listed as an open item and checked against the project's own documentation.

**Question:** Which model do you want the design built around?

**User:** Stop running the skill.

The assistant stopped, asked no more questions, and confirmed that no simulation details had been saved.

## Intake state when the run ended

| Item | Answer |
| --- | --- |
| Objective | Predict streamflow |
| Study area | Logan River, outlet at USGS 10109000 |
| Period | Water year 2026 (2025-10-01 to 2026-09-30) |
| Warm-up | 6 months (2025-04-01 to 2025-09-30) |
| Time step | Hourly |
| Time zone | Observations in Mountain time; other data unspecified |
| Evaluation | USGS observations at 10109000 |
| Existing materials | None |
| Calibration | None, baseline with default parameters |
| Platform | Mac laptop, Apple M1 |
| Model | Not chosen (NGEN via NGIAB suggested first) |
