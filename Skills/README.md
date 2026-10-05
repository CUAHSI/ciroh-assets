# Skills

Finished, reusable skills for hydrologic modeling belong in this directory, with each skill in its own subdirectory.

| Skill | Purpose |
| --- | --- |
| [`hydrologicmodeling`](hydrologicmodeling/SKILL.md) | Model-agnostic design of a hydrologic model simulation. Model-specific notes are in its `references/` folder (currently NGEN). |

Each skill is a `SKILL.md` file with `name` and `description` frontmatter, where `name` matches the directory name.

## Examples

| Example | What it shows |
| --- | --- |
| [Logan River streamflow intake](hydrologicmodeling/examples/logan-river-streamflow.md) | A test of the `hydrologicmodeling` skill: a one-question-at-a-time interview about predicting streamflow at USGS 10109000, ending at the user's request before a design was produced. |
