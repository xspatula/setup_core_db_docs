---
title: "Defining pg_users and their environment files"
layout: single
sidebar:
  nav: "building"
excerpt: "pg_users are created only once, by the superuser, when a database is set up. Every ordinary community.user row logs in through whichever pg_user its stratum_code points to — decide your roster before that first run."
permalink: /building/pg_users/
author_profile: false
date: 2026-08-25
last_modified_at: 2026-08-25
---

Your project needs its own set of pg_users — the postgres-level roles ordinary logins actually run under — defined in your own setup scheme file. This page covers the mechanics of how that works and why it has to be decided up front.

## Where pg_users are defined

In the `postgresdb.db_users` array of your project's own setup scheme file, mirroring the shipped `setup/zzz/scheme_xspatula_local_setup.json`. The full JSON shape, the 8 predefined role names, and how to add one pg_user per role are already documented in [Scheme file][framework_scheme_file] — that page is the syntax reference; this one covers what happens once you run it.

## Only the superuser can create pg_users

[Scheme file][framework_scheme_file] already states this; here's concretely why it's true, not just asserted. Creating postgres roles (`Create_db_roles`) and writing their `.env` credential files (`Create_db_environment_dot`) both happen inside `Initiate_database` — the function driven by `setup_db.ipynb` — and only run after the operator has authenticated as the postgres superuser and confirmed the "Set up database?" prompt. No other code path in the framework creates a pg_user or rewrites an `.env` file. There's no way for an ordinary logged-in user, even a stratum-5 one, to add a pg_user later.

**Practical implication**: decide your full pg_user roster — which of the 8 roles you actually need — before your first `setup_db.ipynb` run. Adding one afterward means running database setup again as the superuser.

## How the .env files actually get created

One file per `db_users` entry, named `.<user_id>.env`, written under `src/postgres/environment/`:

```
DB_NAME=<db>
DB_USER=<user_id>
DB_PASSWORD=<password>
DB_HOST=<host>
DB_PORT=<port>
```

So a `db_users` entry with `"user_id": "user_cat_3"` produces `src/postgres/environment/.user_cat_3.env`. These files are generated, not hand-edited — re-running database setup regenerates them from whatever your scheme file's `db_users` array currently says.

## How an ordinary user links to a pg_user at login

`community.user.stratum_code` is turned into a pg_user name by plain string formatting — `stratum_code = 3` becomes the string `user_cat_3`, and from there the filename `.user_cat_3.env`. There's no lookup table and no validation: if no `.env` file exists for that exact stratum, login fails outright rather than falling back to something less privileged.

This applies identically no matter how the `community.user` row was created — seeded directly (see [Setting up the initial user][building_initial_user]) or self-registered through [Excel intake][building_add_users], which defaults `stratum_code` to `0` if the spreadsheet leaves it blank. Either way, **your pg_user roster has to include an entry for every stratum_code value any of your users will actually have**, decided before setup, per the section above.

Under the hood, logging in is a two-connection handoff: password verification always happens over a fixed `login_evaluation` connection, regardless of who's logging in. Only after that succeeds does a second, separate connection open using the stratum-specific `.env` file's credentials — that second connection is what the rest of the session actually runs under. This is why a missing `.env` file doesn't degrade gracefully: the password check can succeed while the follow-up connection has nothing to connect with.

## A gotcha: there is no user_cat_0 role

Look closely at the shipped scheme file and you'll notice the `db_users` entry for `user_cat_0` has `"role": "user_cat_1"`, not `"role": "user_cat_0"`. That's not a typo — leave it alone. The framework's underlying role/privilege definitions simply don't include a `user_cat_0` entry at all; only `community_admin`, `login_evaluation`, and `user_cat_1` through `user_cat_5` exist. If you changed that entry to `"role": "user_cat_0"`, role creation would silently skip it (printing an error) while the `.env` file would still get written — leaving a stratum-0 user with credentials pointing at a postgres role that was never created, and login failing. As shipped, stratum-0 users simply get `user_cat_1`'s grants.

## stratum_code vs. pg_user tier — two different layers

It's easy to conflate these, since the shipped defaults use the same numbers for both, but they're separate systems:

- **`min_user_stratum`** (covered in [Bootstrapping process management][manage_process]) is an application-level check — which processes a logged-in user is allowed to *call*.
- **The pg_user tier** a `stratum_code` resolves to is a postgres-level role — what the underlying database *connection* is actually permitted to do.

The shipped defaults happen to make these coincide at the top: stratum 5 resolves to `user_cat_5`, which is created `WITH SUPERUSER` — a real postgres superuser, not just "allowed to call `add_root_process`." Below that, privileges scale by tier: `user_cat_1` through `user_cat_4` get connect-only grants (no schema access) by default; `community_admin`/`login_evaluation` get their fixed `community` (and, for `login_evaluation`, `audit`) grants; `user_cat_5` alone gets explicit `utility`/`process` schema grants on top of its superuser status. If your project needs finer-grained access for tiers 1-4 — read access to your own domain schemas, say — that means extending the framework's own role definitions, which is a source-level decision this page doesn't walk through.

## Input files

| File | Purpose |
|---|---|
| `setup/zzz/scheme_<project>_local_setup.json` | Your project's setup scheme file; its `postgresdb.db_users` array defines the pg_user roster |
| `src/postgres/environment/.<user_id>.env` | Generated, one per `db_users` entry — never hand-edit, re-running setup regenerates them |

[framework_scheme_file]: /framework/scheme_file/
[manage_process]: /setup_db/manage_process/
[building_initial_user]: /building/initial_user/
[building_add_users]: /building/add_users/
