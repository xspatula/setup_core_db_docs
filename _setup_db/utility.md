---
title: "Utility Schema"
layout: single
sidebar:
  nav: "setup_db"
excerpt: "The utility schema holds general support tables used across the entire AI4SH database. It is a default Xspatula framework schema and is required by the community schema."
permalink: /setup_db/utility/
author_profile: false
date: 2026-03-31 08:00:00 +0200
last_modified_at: 2026-03-31 08:00:00 +0200
---

The `utility` schema holds general support tables shared across the entire database. It is one of the three default Xspatula framework schemas and must be populated before the `community` schema, because the `community.user` table references `utility.territory`.

## Process files

Two process files create the utility schema content:

| File | Purpose |
|---|---|
| `utility/utility_v10_sql.json` | Creates the utility tables |
| `utility/utility_territory_v10_sql.json` | Inserts territory reference records |

## Tables

### territory

The `territory` table holds country/territory codes used as reference data for users and observations. It provides a standardised way to associate any record with a geographic territory without duplicating geographic metadata in every table.

Key columns:

| Column | Type | Description |
|---|---|---|
| `id` | SERIAL | Primary key |
| `territory_id` | VARCHAR | ISO or custom territory code |
| `name` | VARCHAR | Full territory name |
| `territory_type` | VARCHAR | Classification (e.g. country, region) |

The `utility_territory_v10_sql.json` file pre-loads standard territory records so that the community user table can immediately reference valid territory IDs.

## Role in the database

The utility schema is referenced by:

- `community.user` — links users to a territory
- `observation.campaign_location` — geographic extent of a campaign
- Potentially any other table that needs geographic context

For the full table specification of the default utility schema, see the [core framework documentation][setup_core_db_docs_schemas].


[setup_core_db_docs_schemas]: https://xspatula.github.io/setup_core_db_docs/setup_db/schemas_tables/
