# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

`seed_ai4sh_db_docs` is a documentation site written in Markdown using the Jekyll theme Minimal Mistakes (https://mmistakes.github.io/minimal-mistakes/). The site is an instructions for how to seed the ai4sh (AI4SoilHealth) database using the Xspatula framework introduced in the sibling directory `setup_core_db_docs`.

The Xspatula framework is written in Python and uses JSON files to define all executions and processes. These JSON files are called from Jupyter notebooks. The framework is available:
- As a sibling directory at relative path `../seed_ai4sh_db`
- On GitHub at https://github.com/xspatula/setup_core_db

## Site Architecture

**Theme**: Minimal Mistakes Jekyll theme, version 4.27.3 (local install, not remote)
**URL (production)**: `https://xspatula.github.io/seed_ai4sh_db_docs/docs`
**Search**: Lunr (client-side, full content)

### Collections

| Collection | Directory | Output | Purpose |
|---|---|---|---|

| `setup_db` | `_setup_db/` | true | Step-by-step database setup guide |
| `setup_processes` | `_setup_processes/` | true | Step-by-step database processes setup guide |
| `ai4sh` | `ai4sh/` | false | AI4SoilHealth project case study |

### Navigation Structure

```
Top navbar:
├─ Setup AI4SoilHealth DB   →  _setup_db/
└─ Setup AI4SoilHealth processes   →  _setup_processes/

Setup DB subsection (9 pages):
  introduction, schemas, process, community, utility, observation_utility, observation, landscape, edna

Setup DB processes (6 pages):
    introduction, root_process, community, utility, observation_utility, observation, edna
```

### Key Directories

- `_data/` — `navigation.yml` (site nav) and `ui-text.yml` (theme UI strings)
- `_setup_processes/` — supplementary process documentation
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
2. **AI4SH Database setup** — defining schemas and tables, 9 pages
4. **AI4SH process setup** — defining processses, 6 pages

## Licenses

- **Data**: Creative Commons Attribution (CC-BY)
- **Code**: MIT License

## Related Repositories

| Repo | Relationship |
|---|---|
| `xspatula/setup_core_db` | The framework being documented; sibling directory `../setup_core_db` |
| `mmistakes/minimal-mistakes` | Jekyll theme used for the site |
