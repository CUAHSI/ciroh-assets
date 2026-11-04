# Framework glossary

Scope: terms that apply to any modeling framework, not one model or framework. Terms specific to one framework live in that framework's reference (for NGEN, see [ngen.md](ngen.md)). Model-agnostic terms are in [glossary.md](glossary.md). Details of a single simulation (site numbers, dates, identifiers) belong in the design brief, never here.

| Term                        | Definition                                                                                                                                                                                                                                 | Avoid                          |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------ |
| Framework                   | Software that runs and couples models, handling time, data exchange, and spatial units. **(inference)** No file in this repository defines it.                                                                                             | "Model" for the coupling layer |
| BMI (Basic Model Interface) | A standard interface for initializing, stepping, and exchanging variables with a model. The name, and that CFE and Noah-OWP-Modular implement it, are **verified** in their references. The description of its purpose is **(inference)**. | "API" alone                    |
