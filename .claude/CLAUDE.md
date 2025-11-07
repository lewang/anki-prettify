# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

## Project Overview

Anki-Prettify is a collection of customizable Anki flashcard templates with
modern themes.
The project provides pre-built packages combining card templates (HTML/JS) with
theme styles (CSS compiled from SCSS).

## Development Commands

### Compiling SCSS to CSS

After editing SCSS files, regenerate CSS:

```bash
sass --style=expanded --no-source-map src/styles/scss:src/styles/css
```

**Important:** Always use `--style=expanded` to ensure closing braces are on
their own lines.

### Building Packages

Generate all Anki package files (.apkg):

```bash
cd tools
python build.py [VERSION]
```

This compiles SCSS, updates version numbers, and generates:

- Individual note type packages:
  `themes/{theme}/notetypes/prettify-{theme}-{notetype}.apkg`
- Theme packages (all note types): `themes/{theme}/prettify-{theme}.apkg`
- Master package (all themes): `prettify.apkg`

## Architecture

### Template System

Four note types, each with front/back HTML files in
`src/templates/default/{notetype}/`:

- **basic**: Single card (Front → Back)
- **basic_reverse**: Two cards (Front ⇄ Back)
- **cloze**: Cloze deletion cards
- **image_occlusion**: Image occlusion with canvas

Templates use Anki's templating syntax (`{{Field}}`, `{{#Conditional}}`) plus
embedded JavaScript for tag/deck breadcrumb rendering.

### Theme System

Three themes in `src/styles/scss/`:

- **nord** (Rubik font): Arctic color palette
- **minimal** (Inter font): Clean grayscale with blue accents
- **dracula** (Source Sans Pro font): Dark vibrant palette

Each theme defines CSS variables for colors and preferences.
Light/dark modes via `.card` and `.card.night_mode` selectors.

### Build Process

`tools/build.py` orchestrates:

1. Version management (from GitHub API or CLI arg)
2. SCSS compilation (via sass CLI)
3. Genanki ID persistence (`tools/ids.json` - maintains stable model/deck/note
   IDs)
4. Package generation (using genanki library to create .apkg files)

### Catalog Export Directory

The `catalog-export/` directory contains card exports from the user's actual
Anki collection:

- Directory structure: Each note type has its own directory (e.g., `Basic/`,
  `Cloze/`, `Image Occlusion/`)
- Files: Each file represents a card by name, containing front HTML followed by
  \`\`\`\\n separator, then back HTML
- `catalog-export/style.css`: Shared stylesheet used by all exported cards
- Purpose: Import into Anki using the addon
  https://ankiweb.net/shared/info/2032572419

### Key Files

- `tools/build.py`: Main build orchestration
- `tools/ids.json`: Persisted genanki IDs (required for reproducible builds)
- `src/styles/scss/{theme}.scss`: Theme source (edit here)
- `src/styles/css/{theme}.css`: Compiled CSS (generated, don't edit directly)
- `src/styles/css/nord-showcase.html`: Visual preview for manual testing

## Important Design Decisions

**SCSS Variables**: Each theme uses SCSS variables for color palettes, but
output is pure CSS (no runtime SCSS).

**Stable IDs**: Genanki requires persistent model/deck/note IDs to prevent
duplicate decks on reimport.
IDs are stored in `tools/ids.json` and auto-generated for new themes/note types.

**Minimal JavaScript**: Templates use vanilla JS only (no frameworks) for Anki
compatibility.

**Template Independence**: Each note type has separate HTML files rather than
shared templates for independent customization.
