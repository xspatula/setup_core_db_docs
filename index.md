---
layout: home
author_profile: true
excerpt: "The EU funded project AI4SoilHealth (AI4SH) database is built for holding soil data derived both from field sampling and satellite images. To accommodate this data with FAIR principles the Xspatula framework was used to build a comprehensive postgreSQL database. This site documents how to seed that database."
---

# Building the AI4SH DB with Xspatula

The EU funded project AI4SoilHealth (AI4SH) database is built for holding soil data derived both from field sampling and satellite images, with a large number of different instruments, laboratories, sensors and methods being used for capturing the data. To accommodate this data with [FAIR (Findability, Accessibility, Interoperability, and Reuse)][fair] principles the [Xspatula framework][setup_core_db_docs_framework] was used to build a comprehensive postgreSQL database.

## Prerequisites

To seed the AI4SH database you must first have the Xspatula framework installed and a postgreSQL database running. These steps are described in the companion site [Setup core db][setup_core_db_docs]. This site assumes you have completed those steps.

You also need to clone or download the AI4SH seed package from GitHub:

```bash
git clone https://github.com/xspatula/seed_ai4sh_db
```

## Outline of the AI4SH postgreSQL database

The AI4SH postgres database contains 9 schemas:

- **utility** — support tables for general information used across schemas (default framework schema)
- **community** — organisations and users; all users logging into the system must be registered here (default framework schema)
- **process** — all processes defined for the AI4SH database (default framework schema)
- **landscape_utility** — reference tables for landscape classification
- **landscape** — landscape observations
- **observation_utility** — catalogues and reference data required for FAIR-compliant soil observations (units, methods, instruments, taxa, etc.)
- **observation** — actual soil property data, organised through datasets, campaigns, samples and observations
- **edna_utility** — reference tables for environmental DNA methods
- **edna** — eDNA observations

## Seeding the database

The AI4SH database is seeded in two stages:

1. **[Setup DB][setup_db]** — defines all schemas and tables using the Jupyter notebook `setup/setup_db.ipynb`
2. **[Setup processes][setup_process]** — registers all framework processes in the database using the notebook `setup/setup_processes.ipynb`

Both stages use the Xspatula JSON-driven workflow: a _scheme file_ points to a _job file_, which links to a _pilot file_ listing the individual _process files_ to execute. For a detailed explanation of this hierarchy, see the [Xspatula framework documentation][setup_core_db_docs_framework].

## Schematic data entry flow

For entering soil property data the entire chain from dataset → campaign → sampling log → sample → observation must be complete. If any link in this chain is missing the data cannot be entered into the database. The observation_utility schema provides all the reference catalogues (units, methods, instruments, etc.) that observations depend on.

## Acknowledgments and Funding

This work was done as part of the AI4SoilHealth project, funded by the European Union's Horizon Europe Research and Innovation Programme under Grant Agreement No. 101086179.

_Funded by the European Union. The views expressed are those of the authors and do not necessarily reflect those of the European Union or the European Research Executive Agency._


[fair]: https://www.go-fair.org/fair-principles/
[setup_core_db_docs]: https://xspatula.github.io/setup_core_db_docs/
[setup_core_db_docs_framework]: https://xspatula.github.io/setup_core_db_docs/framework/
[setup_db]: /setup_db/
[setup_process]: /setup_process/
