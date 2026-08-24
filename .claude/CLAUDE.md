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
| `audit` | `_audit/` | true | Step-by-step guide for including an audit system |
| `community` | `_community` | true | Step-by-step guide for adding organisations and users via excel files, hash crypted passwords and SMTP emailing |
| `building` | `_building` | true | Step-by-step guide for building a new xspatula framework |

### Navigation Structure

```
Top navbar:
├─ Xspatula framework  →  /framework/
├─ Setup DB            →  /setup_db/
├─ Setup processes     →  /setup_processes/
├─ Add user data       →  /user_data/
├─ Audit     →  /audit/
├─ Community     →  /setup_community/
└─ Building     →  /building/

Framework subsection (7 pages):
  synopsis, notebook, scheme_file, job_file, pilot_file, process_file, vscode

Setup DB subsection (5 pages + anchor):
  synopsis, postgres, anaconda, netrc, schemas_tables (+ #table-insert anchor)

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

### Notes on Setup DB
Before you run the setup_db notebook, you must define your initial users in setup/zzz/xspatula/setup_db/json_core/community_user_records_v10_sql.json and then change the creator in setup/zzz/xspatula/setup_db/json_core/processes_records_v10_sql.json to one of the these users, otherwise the script will return an error and not insert the process. As all user passwords are hash crypted you must run a CLI to generate the hash crypted version of you chosen password and enter that crypted version in the JSON file inserting your user.

#### CLAUDE task
The password cryptation has been added since the previous version - see the sibling repo `xspatula_ai4sh_docs` for the specific manual for the project ai4sh and generalise and expand the instructions for this project. Setup DB is now also linked to the automated definition of auditing - see both below and the sibling repo `xspatula_ai4sh_docs` and include it as an introduction under setup DB with full instructions under the new collection `auditing` (below).

### Notes on Setup processes
Setup processes has not changed, but the instructions need to be expanded with one more page on process_options

#### CLAUDE task
Create one more page under Setup process that details the additional options process_parameter_inherit and process_parameter_auto_name. Make summary table of all the database tables belonging to setup processes and what there objectives are.

### Notes on Add user data
Adding user data has been expanded to 1-step route, complementing the hitherto existing 2-step route.

#### CLAUDE task
Look at the sibling repo `xspatula_ai4sh_docs` to understand the new 1-step process (code is already implemented in the repo `core_setup_db`). Expand and generalise the instructions from `xspatula_ai4sh_docs` in this repo to make it easier for "dummies" to understand.

### Notes on Audit
Audit is completely new collection, I just copied the instructions as it is written in `xspatula_ai4sh_docs`.

#### CLAUDE task
The audit collection is not yet linked to any other parts of this repo and that must be done. The audit collection itself must also be generalised (not only for AI4SH, but for any project) and expanded for "dummies" to understand.

#### Notes on community
Community is completely new collection, I just copied the instructions as it is written in `xspatula_ai4sh_docs`.

#### CLAUDE task

The community collection is not yet linked to any other parts of this repo and that must be done. The community collection itself must also be generalised (not only for AI4SH, but for any project) and expanded for "dummies" to understand.

### Notes on Building
The building collection does not yet exist, it must be created and populated with 9 pages that step wise takes a novel user through what is required to build a new project and its database. The following 9 pages are my suggestion:

1. project -> naming a new project and which notebooks and python modules this affects (= every notebook where the call to Run_process function that always sits under the project specific module under `src` as well as any `scheme_file`, there must then also be a new [renamed] module with every project that is the starting point for running any process).
2. notebooks -> where to edit the notebooks in any new project (the line `from src.example_project import Run_process` must be changed to `from src.<project_module> import Run_process`) - if you compare `src/example_project/process.py` in this repo with `src/ai4sh/process.py` in the sibling folder `xspatula_ai4sh` you understand.
3. database -> for any project with any real content the creator must setup schemas and tables for all processes that he/she wants the project to perform. Use `xspatula_ai4sh`, and expand on the way I have added all additional postgres functions outside the default postgres modules in that repo - including the way I hook them in with the general postgres connection in an odd way that requires expansion in `xspatula_ai4sh` ai4sh application modules with the code line `from src.postgres.pg_ai4sh import PG_manage_AI4SH` that is then accessed via the codeline `self.pg_ai4sh_C = PG_manage_AI4SH(pg_session_C)`. I then put all project specific postgres processes in a separate folder (with the same name as the project). This allows me to keep the core postgres functions untouched and reuseable.
4. auditing -> detailed instructions for how to set up the auditing starting with defining the tables to audit when the tables are setup and how the JSON command files and the pilot file for audit is automatically created and can then be (optionally) run from the notebook `setup/setup_db.ipynb`.
5. Setting up the initial user demands setting the hash crypted password in the JSON file defining users at startup, which in turn requires running the CLI `setup/hash_password.py`. Make this detailed to the level of opening a terminal window and command to write to get the password hash encryptation.
6. SMTP setup for automatic email sending to users added at later stages, see sibling repo `xspatula_ai4sh_docs` and expand for "dummies"
7. How to actually add additional organsiations and users via excel, see sibling repo `xspatula_ai4sh_docs` and expand for "dummies"
8. How to write a new root process and associated process definition for the database and how to add functions that relate to these new processes, use a root process + processes from `xspatula_ai4sh` and expand on where in the system the new processes is identified and how to link in a new module that defines what that process should do. My sugestion is that you use the processes definition `xspatula_ai4sh/setup/zzz/ai4sh/setup_processes/json_ai4sh/plot/plot_v10_sql.json` that contains both the root process and several processes (skip most parameters, just keep a few for illustration) and is implemented under `xspatula_ai4sh/src/ai4sh/plot.py` - how is it done to link the database definition to the module and its functions (all steps, generic)
9. summarise the edits required to add postgres funtionality for an expanded project and where that happens (table with modules to edit)


## Related Repositories

| Repo | Relationship |
|---|---|
| `xspatula/xspatula.github.io` | The framework landing page; sibling directory `../xspatula.github.io` |
| `xspatula/setup_core_db` | The framework being documented; sibling directory `../setup_core_db` |
| `xspatula_ai4sh_docs` | Documentation for an xspatula based project with a much larger database and additional functions |
| `xspatula_ai4sh` | xspatula project documented in `xspatula_ai4sh_docs` |


## Important notes

- Documentation should be generic and useful for any project using the xspatula framework, not just the national-territories example project.
- The example project (`project_example`) translates Excel data on national territories to JSON format that is then entered into the database. It is used as the concrete example throughout `setup_processes` and `user_data` sections.
- For every page produced, add a list of all input files (Excel, JSON, etc.) at the end, stating each file's content and purpose.
- `_setup_processes/user_scheme_file.md` is a reference/draft note explaining the shift from superuser to a category-5 user after initial DB setup. It is **not** a site page — do not include it in any collection or nav.
- The earlier versions of this site is archived in `.claude/CLAUDE_version-1.md` and `.claude/CLAUDE_version-2.md`.
