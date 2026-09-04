# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

`setup_core_db_docs` is a documentation site written in Markdown using the Jekyll theme Minimal Mistakes (https://mmistakes.github.io/minimal-mistakes/). The site introduces the Xspatula framework and guides users through setting up a PostgreSQL database with it.

The Xspatula framework is written in Python and uses JSON files to define all executions and processes. These JSON files are called from Jupyter notebooks. The framework is available:
- As a sibling directory at relative path `../setup_core_db`
- On GitHub at https://github.com/xspatula/setup_core_db

## Site Architecture

**Theme**: Minimal Mistakes Jekyll theme, version 4.27.3 (local install, not remote)
**URL (production)**: `https://xspatula.github.io/setup_core_db_docs` (url + baseurl from `_config.yml`)
**Search**: Lunr (client-side, full content)

### Collections

| Collection | Directory | Output | Purpose |
|---|---|---|---|
| `framework` | `_framework/` | true | Framework architecture documentation |
| `setup_db` | `_setup_db/` | true | Step-by-step database setup guide |
| `setup_processes` | `_setup_processes/` | true | Step-by-step guide for setting up a process example (translating and adding tabular data to the database)|
| `user_data` | `_user_data/` | true | Step-by-step guide for adding user specific tabular (excel) data via JSON conversion |
| `auditing` | `_auditing/` | true | Step-by-step guide for including an audit system |
| `setup_community` | `_setup_community/` | true | Step-by-step guide for adding organisations and users via excel files, hash crypted passwords and SMTP emailing |
| `building` | `_building/` | true | Step-by-step guide for building a new xspatula framework |

### Navigation Structure

```
Top navbar:
├─ Xspatula framework  →  /framework/
├─ Setup DB            →  /setup_db/
├─ Setup processes     →  /setup_processes/
├─ Add user data       →  /user_data/
├─ Audit     →  /auditing/
├─ Community     →  /setup_community/
└─ Building     →  /building/

Framework subsection (7 pages):
  synopsis, notebook, scheme_file, job_file, pilot_file, process_file, vscode

Setup DB subsection (5 pages + anchor):
  synopsis, postgres, anaconda, netrc, schemas_tables (+ #table-insert anchor), manage_process

Setup processes subsection (5 pages):
  synopsis, scheme_file, define_process, edit_process, process_options

Add user data subsection (3 pages):
    synopsis, translate_excel, insert_data

Audit subsection (3 pages):
  introduction, setup, queries

Community subsection (6 pages):
  introduction, setup, queries

Building subsection (9 pages):
  project, notebooks, database, auditing, initial user(s), SMTP, more users, processes, postgres
```

### Setup DB - already done

In setup DB one more page is require - a detailed outline of how the initial root process `manage_process` is defined - the setting of the stratum_code that is allowed to manage root process is especially important. The same goes for how the initial process `process` is defined and the stratum_code required for managing a `process`. These processes are defined in the file    `setup/zzz/xspatula/setup_db/json_core/process/processes_records_v10_sql.json`, that so to say hands over the right from the superuser to the database users with at least the defined stratum_code levels to add new root process and processes. I think using excerpts from the file `setup/zzz/xspatula/setup_db/json_core/process/processes_records_v10_sql.json` is a good idea to show how it works.

### Building - already done

The building collection requires one more page -  a details ouline of how to define pg_users in the scheme file for setup_db `setup/zzz/scheme_xspatula_local_setup.json` in the code repo. The page should explain how the environment files for the defined pg_users are created at runtime and how they link to ordinary database users defined either during setup_db or with the process `manage_user` (from excel or csv files). Insert this new page after page `initial_user`in the Building collection and include the fact that only the superuser can create new pg_users (see `_framework/scheme_file.md` in this repo).

### Building 2

After the previous session/run (the ### Building - already done, above) I discovered that I had missed a code change in the code repo under `setup/src_setup/lib_setup/setup_db.py`. I changed the hardcoded dictionaries `REVOKE_D` and `ROLES_D`to be read from 2 JSON files (see code). This change allows users to easier define and revoke roles. Please update the section `stratum_code vs. pg_user tier — two different layers` in the file `_building/pg_users.md` and add instructions for how to edit and revoke roles to this page.

## Important notes

- Documentation should be generic and useful for any project using the xspatula framework, not just the national-territories example project.
- The example project (`project_example`) translates Excel data on national territories to JSON format that is then entered into the database. It is used as the concrete example throughout `setup_processes` and `user_data` sections.
- For every page produced, add a list of all input files (Excel, JSON, etc.) at the end, stating each file's content and purpose.
- `_setup_processes/user_scheme_file.md` is a reference/draft note explaining the shift from superuser to a category-5 user after initial DB setup. It is **not** a site page — do not include it in any collection or nav.
- The earlier version of this site is archived in `.claude/CLAUDE_version-1.md`.
