---
title: "Observation Processes"
layout: single
sidebar:
  nav: "setup_processes"
excerpt: "Observation processes register all data management operations for the observation schema — datasets, campaigns, sampling logs, samples, observations and specialised measurement types."
permalink: /setup_process/observation/
author_profile: false
date: 2026-03-31 08:00:00 +0200
last_modified_at: 2026-03-31 08:00:00 +0200
---

Observation processes register the operations for managing data in the `observation` schema. These processes follow the same data hierarchy as the tables themselves: you must be able to manage datasets before campaigns, campaigns before sampling logs, and so on down to individual observations.

## Process files

| File | Process registered | Target table(s) | Min stratum |
|---|---|---|---|
| `observation_organisation_v10_sql.json` | `manage_data_owner` | `observation.data_owner` | 4 |
| `observation_person_v10_sql.json` | `manage_person` | `observation.person` | 4 |
| `observation_dataset_v10_sql.json` | `manage_dataset` | `observation.dataset` | 3 |
| `observation_campaign_v10_sql.json` | `manage_campaign` | `observation.campaign` + companion tables | 3 |
| `observation_geolocation_v10_sql.json` | `manage_geolocation` | `observation.sample_geolocation` | 3 |
| `observation_sampling_log_v10_sql.json` | `manage_sampling_log` | `observation.sampling_log` | 3 |
| `observation_sample_v10_sql.json` | `manage_sample` | `observation.sample` | 3 |
| `observation_sample_image_v10_sql.json` | `manage_sample_image` | `observation.macrofauna_image` | 3 |
| `observation_observation_log_v10_sql.json` | `manage_observation_log` | `observation.observation_log` | 3 |
| `observation_observation_v10_sql.json` | `manage_observation` | `observation.observation` | 3 |
| `observation_quantity_v10_sql.json` | `manage_observation_quantity` | `observation.observation` (quantity-specific) | 3 |
| `observation_measurement_v10_sql.json` | `manage_measurement` | `observation.measurement` | 3 |

## Access levels

Data owner and person management requires stratum 4 (administrative). All other observation processes require stratum 3, reflecting that data entry and management is a regular scientific operation but still requires a level of trust above basic read-only access.

## Key processes in detail

### manage_dataset

A dataset is the top-level grouping of related data. Required parameters:

- `data_owner_id__owner_name` — the organisation owning the dataset
- `name` — unique dataset name
- `alias` — short identifier (e.g. "AI4SH", "LUCAS")

### manage_campaign

Registers a campaign (child of a dataset) and its metadata. The `manage_campaign` process handles the core `campaign` table. Companion meta tables (`campaign_meta`, `campaign_method_tier`, `campaign_location`, `campaign_provision`, `campaign_setting`) may have separate processes or be populated as part of the campaign registration.

Required parameters include:
- `dataset_id__dataset_name` — the parent dataset
- `name` — campaign name (automatically constructed from dataset alias and date in some configurations)
- `contact_name`, `contact_email`

### manage_sampling_log

Registers a sampling event within a campaign. A sampling log is the bridge between the organisational structure (campaign) and individual physical samples.

Required parameters: `campaign_id`, `person_id`, start and end date, and method references.

### manage_sample

Registers an individual physical sample. Links to a `sampling_log` and optionally to a `sample_geolocation` for spatial referencing.

### manage_observation_log and manage_observation

`manage_observation_log` registers the connection between a sample and the provision used to analyse it, along with sample handling logistics (preservation, storage, transportation). `manage_observation` then stores the actual measured value for a specific `provision_indicator`.

The key parameter in `manage_observation` is the indicator value itself — a numeric or text result that must be consistent with the unit defined in the `provision_indicator`.

### manage_geolocation

Registers spatial coordinates for a sample:

- `latitude_dd_wgs84` — decimal degrees latitude
- `longitude_dd_wgs84` — decimal degrees longitude
- `elevation` — optional elevation in the specified unit
- `spatial_reference_id` — FK to `observation_utility.spatial_reference`

### manage_measurement

Registers a direct instrument reading (e.g. from a handheld spectral sensor). This is a lighter-weight observation type for sensor output that does not go through the full observation_log chain.

## Data entry sequence

To enter a complete soil observation, the following sequence must be followed:

1. `manage_data_owner` (if the organisation is new)
2. `manage_dataset`
3. `manage_campaign`
4. `manage_sampling_log`
5. `manage_sample` (optionally with `manage_geolocation`)
6. `manage_observation_log`
7. `manage_observation`

If any step in this chain is missing, the foreign key constraints prevent entry of the subsequent records. This enforced chain is what ensures FAIR data compliance — every observation can be traced back to a fully documented campaign and sampling event.
