---
title: "Utility Processes"
layout: single
sidebar:
  nav: "setup_processes"
excerpt: "Utility schema processes provide bulk import capabilities for tabular data and unit definitions. Two process registration files handle these import workflows."
permalink: /setup_process/utility/
author_profile: false
date: 2026-03-31 08:00:00 +0200
last_modified_at: 2026-03-31 08:00:00 +0200
---

The utility process files in the AI4SH `setup_processes` workflow register import processes that allow reference data to be loaded from external tabular sources (spreadsheets, CSV files) rather than entered record by record through individual process calls.

## Process files

Two files are registered at the root of `setup_processes/json_ai4sh/` and cover cross-schema import utilities:

| File | Process registered | Description |
|---|---|---|
| `import_tabular_root_v10_sql.json` | `import_tabular_root` | Root process for bulk tabular data import operations |
| `import_unit_v10_sql.json` | `import_unit` | Imports unit definitions from a tabular source into `observation_utility.unit` |

## import_tabular_root

This file registers the root process family for all bulk import operations. Any process that loads reference data from an external tabular file (spreadsheet or CSV) is grouped under this root. Registering the root before the individual import processes follows the same pattern as `manage_table_data` and `translate_data`.

## import_unit

The `import_unit` process reads unit definitions from a tabular source and inserts them into `observation_utility.unit`. This is more efficient than registering units one by one when loading a large standard unit library.

A typical call to `import_unit` requires:

- The path to the source tabular file
- The column mapping (which columns in the source correspond to `name`, `symbol`, `quantity_id`, etc.)
- An overwrite flag (whether to update existing units or skip them)

## When to use bulk import

For most reference catalogues in `observation_utility`, a modest number of curated records is inserted during the setup_processes stage. For `unit`, however, a comprehensive standard library may include hundreds of SI, derived and domain-specific units. Bulk import avoids writing hundreds of individual process calls and instead reads from a maintained reference spreadsheet.

The same `import_tabular` mechanism can be extended to other catalogues where a curated external source is the most practical way to populate reference data at scale.
