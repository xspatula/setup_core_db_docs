# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

`setup_core_db_docs` is a documentation site written in Markdown using the Jekyll theme Minimal Mistakes (https://mmistakes.github.io/minimal-mistakes/). The site introduces the Xspatula framework and guides users through setting up a PostgreSQL database with it.

The Xspatula framework is written in Python and uses JSON files to define all executions and processes. These JSON files are called from Jupyter notebooks. The framework is available:
- As a sibling directory at relative path `../setup_core_db`
- On GitHub at https://github.com/xspatula/setup_core_db

## Site Architecture

**Theme**: Minimal Mistakes Jekyll theme, version 4.27.3 (local install, not remote)
**URL (production)**: `https://xspatula.github.io/setup_core_db_docs/docs`
**Search**: Lunr (client-side, full content)

### Collections

| Collection | Directory | Output | Purpose |
|---|---|---|---|
| `framework` | `_framework/` | true | Framework architecture documentation |
| `setup_db` | `_setup_db/` | true | Step-by-step database setup guide |
| `ai4sh` | `ai4sh/` | false | AI4SoilHealth project case study |

### Navigation Structure

```
Top navbar:
├─ Xspatula framework  →  _framework/
└─ Setup Xspatula DB   →  _setup_db/

Framework subsection (7 pages):
  introduction, notebook, scheme_file, job_file, pilot_file, process_file, vscode

Setup DB subsection (5 pages):
  introduction, postgres, anaconda, netrc, schemas_tables
```

### Key Directories

- `_data/` — `navigation.yml` (site nav) and `ui-text.yml` (theme UI strings)
- `_setup_processes/` — supplementary process documentation (not in main nav yet)
- `assets/media/` — logos and schema diagram images
- `_site/` — generated output (do not edit manually)

## Configuration Files

- `_config.yml` — production config; sets site title, URL, collections, plugins
- `_config_local.yml` — local dev override; sets empty URL/baseurl for local serving
- `Gemfile` — single gem dependency: `minimal-mistakes-jekyll 4.27.3`

To serve locally: `bundle exec jekyll serve --config _config.yml,_config_local.yml`

## Content Conventions

- All documentation pages use Jekyll front matter with at minimum: `layout`, `title`, `categories`, `tags`
- Sidebar navigation is set via `sidebar: { nav: "<nav_key>" }` in front matter
- Internal links use Jekyll's `{{ site.baseurl }}` or relative paths
- Code examples are fenced with language identifiers (python, json, sql, bash)
- Database schema diagrams are stored in `assets/media/` and referenced in markdown

## Content Scope

The documentation covers:
1. **Framework fundamentals** — scheme files, job files, pilot files, process files, notebook interface
2. **Database setup** — PostgreSQL installation, Anaconda environment, `.netrc` credentials, defining schemas and tables
3. **Security** — use of `.netrc` and `.env` files; six user categories (`user_cat_0` through `user_cat_5`) with varying PostgreSQL privileges
4. **AI4SH case study** — EU-funded AI4SoilHealth project using six database schemas: utility, community, process, observation_utility, observation

## Licenses

- **Data**: Creative Commons Attribution (CC-BY)
- **Code**: MIT License

## Related Repositories

| Repo | Relationship |
|---|---|
| `xspatula/setup_core_db` | The framework being documented; sibling directory `../setup_core_db` |
| `mmistakes/minimal-mistakes` | Jekyll theme used for the site |
