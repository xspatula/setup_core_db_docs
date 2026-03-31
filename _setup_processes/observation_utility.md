---
title: "Observation Utility Processes"
layout: single
sidebar:
  nav: "setup_processes"
excerpt: "Observation utility processes register operations for managing all reference catalogues in the observation_utility schema — apparatus, indicators, methods, units, provisions and more. These must be registered before any observation data can be entered."
permalink: /setup_process/observation_utility/
author_profile: false
date: 2026-03-31 08:00:00 +0200
last_modified_at: 2026-03-31 08:00:00 +0200
---

Observation utility processes register the operations for managing every reference catalogue in the `observation_utility` schema. Each process allows authorised users to insert, update, or delete records in a specific catalogue table. These processes must be registered before any observation data management processes, because observation processes depend on the utility catalogues being populated and manageable.

## Process files

| File | Process registered | Target table | Min stratum |
|---|---|---|---|
| `observation_utility_apparatus_v10_sql.json` | `manage_apparatus` | `observation_utility.apparatus` | 4 |
| `observation_utility_classification_v10_sql.json` | `manage_classification` | `observation_utility.order/family/genus/species/specimen` | 4 |
| `observation_utility_indicator_v10_sql.json` | `manage_indicator` | `observation_utility.indicator` | 4 |
| `observation_utility_indicator_default_unit_v10_sql.json` | `manage_indicator_default_unit` | `observation_utility.indicator_default_unit` | 4 |
| `observation_utility_juxtaposition_v10_sql.json` | `manage_juxtaposition` | `observation_utility.juxtaposition` | 4 |
| `observation_utility_license_v10_sql.json` | `manage_license` | `observation_utility.license` | 4 |
| `observation_utility_location_method_v10_sql.json` | `manage_location_method` | `observation_utility.location_method` | 4 |
| `observation_utility_method_tier_v10_sql.json` | `manage_method_tier` | `observation_utility.method_tier` | 4 |
| `observation_utility_method_translate_v10_sql.json` | `manage_method_translate` | `observation_utility.analysis_method_translate` | 4 |
| `observation_utility_method_v10_sql.json` | `manage_method` | `observation_utility.analysis_method` | 4 |
| `observation_utility_preparation_v10_sql.json` | `manage_preparation` | `observation_utility.preparation` | 4 |
| `observation_utility_preservation_v10_sql.json` | `manage_preservation` | `observation_utility.preservation` | 4 |
| `observation_utility_profiling_v10_sql.json` | `manage_profiling` | `observation_utility.profiling` | 4 |
| `observation_utility_provider_v10_sql.json` | `manage_provider` | `observation_utility.provider` | 4 |
| `observation_utility_provision_indicator_v10_sql.json` | `manage_provision_indicator` | `observation_utility.provision_indicator` | 4 |
| `observation_utility_provision_serial_nr_v10_sql.json` | `manage_provision_serial_nr` | `observation_utility.provision` (serial number field) | 4 |
| `observation_utility_provision_v10_sql.json` | `manage_provision` | `observation_utility.provision` | 4 |
| `observation_utility_quantity_default_unit_v10_sql.json` | `manage_quantity_default_unit` | `observation_utility.quantity_default_unit` | 4 |
| `observation_utility_quantity_v10_sql.json` | `manage_quantity` | `observation_utility.quantity` | 4 |
| `observation_utility_setting_v10_sql.json` | `manage_setting` | `observation_utility.juxtaposition` (setting records) | 4 |
| `observation_utility_spatial_reference_v10_sql.json` | `manage_spatial_reference` | `observation_utility.spatial_reference` | 4 |
| `observation_utility_storage_v10_sql.json` | `manage_storage` | `observation_utility.storage` | 4 |
| `observation_utility_transportation_v10_sql.json` | `manage_transportation` | `observation_utility.transportation` | 4 |
| `observation_utility_unit_translate_v10_sql.json` | `manage_unit_translate` | `observation_utility.unit_translate` | 4 |
| `observation_utility_unit_v10_sql.json` | `manage_unit` | `observation_utility.unit` | 4 |

## Access level

All observation utility management processes require a minimum user stratum of 4. This reflects that catalogue management (adding new instrument types, methods, units, etc.) is an administrative-level operation, not something an ordinary data contributor should be able to do. Incorrect or inconsistent catalogue entries would break the referential integrity of all observations that reference them.

## Key processes in detail

### manage_apparatus

Registers a new type of apparatus — any instrument, tool, laboratory process, or service that can deliver observation data. Parameters:

- `name` — generic apparatus name (e.g. "VNIR spectroscopy", "wet chemistry laboratory")
- `alias` — common shorthand (e.g. "wetlab")
- `abstract` — plain language description

The `alias` and `abstract` can be updated; the `name` cannot (it is a permanent identifier).

### manage_provision

A provision combines an apparatus, a provider, and a method tier into a single identifiable combination. This is the key linking record that connects "what instrument" + "who operates it" + "at what professionality level" into a single entity that observations can reference. Parameters:

- `apparatus_id__apparatus_alias` — look up apparatus by alias
- `provider_id__provider_alias` — look up provider by alias
- `method_tier_id__method_tier_alias` — look up method tier by alias

### manage_provision_indicator

Links a provision to the specific indicators (measurable values) it delivers, together with the analysis method and unit. This is the record an observation references when storing an actual measurement value. Parameters:

- `provision_id` — the provision delivering this indicator
- `indicator_id__indicator_name` — the indicator being measured
- `method_id__method_alias` — the analysis method used
- `unit_id__unit_symbol` — the unit of the reported value

### manage_indicator

Registers a measurable soil property. An indicator belongs to a `quantity` (the physical property type) and has a name, alias, and abstract. The combination of indicator + provision_indicator defines exactly what value an observation stores.
