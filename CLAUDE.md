# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a customized **Antora Default UI** — a UI bundle for [Antora](https://antora.org/) documentation sites, forked from the OpenDevise default UI and modified for OpenNMS projects. The build output is a `ui-bundle.zip` that Antora consumes to render documentation.

## Commands

```bash
npm install          # Install dependencies (Node >= 8, prefer Node 10 per .nvmrc)

gulp preview         # Build UI and serve preview at http://localhost:5252 with live reload
gulp bundle          # Full production build: clean → lint → build → pack ui-bundle.zip
gulp bundle:pack     # Pack bundle without cleaning (preserves preview site)
gulp build           # Build and stage UI assets without packing

gulp lint            # Run both CSS and JS linters
gulp lint:css        # Stylelint on CSS
gulp lint:js         # ESLint on JavaScript
gulp format          # Format JS with prettier-eslint

gulp clean           # Remove build/ and public/ directories
```

There are no unit tests — the `preview` task is how you validate changes.

## Architecture

### Build System

Gulp 4 (`gulpfile.js` + `gulp.d/tasks/`) drives the entire pipeline. Each task is a separate module in `gulp.d/tasks/`. The build:
1. Compiles CSS with PostCSS (autoprefixer, cssnano) from `src/css/site.css`
2. Bundles JS with Browserify from each file in `src/js/`
3. Compiles Handlebars layouts/partials from `src/layouts/` and `src/partials/`
4. Copies images from `src/img/`
5. Packs everything into `build/ui-bundle.zip`

### Source (`src/`)

| Directory | Purpose |
|-----------|---------|
| `css/` | Modular CSS — imported via `site.css`; covers base, nav, header, footer, article, code, tabs, etc. |
| `js/` | Client-side modules (nav, on-this-page scroll, fragment-jumper, page-versions, mobile navbar, tabset) — each becomes a separate bundle |
| `layouts/` | Two Handlebars page layouts: `default.hbs` and `404.hbs` |
| `partials/` | ~29 Handlebars partials for all page regions (header, footer, nav, breadcrumbs, article, etc.) |
| `helpers/` | Custom Handlebars helpers (`and`, `detag`, `eq`, `increment`, `not`, `or`, `relativize`, `year`) |
| `img/` | Static image assets |

### Preview (`preview-src/`)

Sample AsciiDoc pages and a `ui-model.yml` that feeds the local preview server. Modify these to test UI changes against realistic content without running a full Antora build.

### Linting Rules

- **JS**: ESLint extending `standard` style, 120-char max line length, arrow-parens always required, multiline comma-dangle
- **CSS**: Stylelint extending `stylelint-config-standard`

## Key Customizations vs. Upstream

- OpenNMS branding in header/footer partials
- Custom copyright year logic in `src/helpers/year.js`
- Font choices (Roboto, Roboto Mono)
- Version-specific release naming (see recent commits for codename pattern)
