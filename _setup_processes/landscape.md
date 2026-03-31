---
title: "Landscape Processes"
layout: single
sidebar:
  nav: "setup_processes"
excerpt: "Landscape processes register operations for managing landscape-level observations that provide environmental context for soil measurements — land use, land cover, terrain and related attributes."
permalink: /setup_process/landscape/
author_profile: false
date: 2026-03-31 08:00:00 +0200
last_modified_at: 2026-03-31 08:00:00 +0200
---

Landscape processes register the operations for managing data in the `landscape` and `landscape_utility` schemas. These processes allow authorised users to populate landscape classification reference catalogues and record landscape observations at sampling sites.

## Process files

The landscape process files are located at:

```
./setup/zzz/ai4sh/setup_processes/json_ai4sh/landscape/
```

Process files in this directory register operations for:

- Managing landscape classification reference records (landscape_utility tables)
- Managing landscape observations linked to sampling sites

## Relationship to observation processes

Landscape observations are recorded at the same sampling locations as soil physicochemical observations but through a separate set of processes. A sampling site documented through the `observation` schema (campaign → sampling log → sample) can also have landscape observations registered through the `landscape` schema processes.

This means that for any sampling site you can query:

- What soil properties were measured (via `observation`)
- What the landscape looked like at the time of sampling (via `landscape`)

The combination allows analyses that relate soil properties to landscape context — a core analytical goal of the AI4SH project.

## Access level

Landscape observation management requires a minimum user stratum of 3, consistent with other observation management processes. Landscape utility catalogue management (reference tables) requires stratum 4.

## Extending the landscape schema

The AI4SH landscape classification system is designed to be extendable. If your pilot site uses a classification system not represented in the default `landscape_utility` catalogues, add new reference records through `manage_landscape_utility` processes before attempting to register landscape observations that reference them. The foreign key constraints will reject landscape observations that reference non-existent catalogue records.
