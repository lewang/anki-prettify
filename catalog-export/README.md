# Catalog Export

This directory contains card templates exported in a format compatible with the
[Templates Import/Export for Anki](https://ankiweb.net/shared/info/1130800042)
addon, which allows importing and exporting CSS and card templates.

## Directory Structure

```
catalog-export/
├── README.md                    # This file
├── style.css                    # Nord theme styles (copied from src/styles/css/nord.css)
├── common_epilog.html           # Shared JavaScript for all cards
├── Basic/
│   └── Card 1                   # Basic card front/back templates
├── Basic (and reversed card)/
│   ├── Card 1                   # Forward card templates
│   └── Card 2                   # Reverse card templates
├── Cloze/
│   └── Cloze                    # Cloze deletion card templates
├── Image Occlusion/
│   └── Image Occlusion          # Image occlusion card templates
└── Vocabulary/
    ├── Forward                  # Term → Definition card
    └── spelling                 # Definition → Term card (with spelling test)
```

## File Format

Each card template file contains the front and back templates separated by
triple backticks (````):

```
<div class="prettify-flashcard">
  <!-- Front template HTML -->
</div>
```
<div class="prettify-flashcard">
  <!-- Back template HTML -->
</div>
```

## Template Features

### Bottom Status Container
All cards include a bottom status bar with:
- **Tags**: Clickable tags displayed on the left
- **Card Info**: Review count (r) and lapse count (l) on the right

### Hint Toggle (Vocabulary Cards Only)
The Forward and spelling vocabulary cards include hint functionality for
card-type-2 (normal cards):
- Images and term fields are hidden behind "show hint" buttons
- Clicking reveals the content
- Implemented via `.hint-container` and `.show-hint` CSS classes
- JavaScript handlers in `common_epilog.html`

### Card Type Tracking
Templates use `{{info-Type:}}` to track card state:
- 0 = suspended
- 1 = learning
- 2 = normal
- 3 = relearning

## Using with Templates Import/Export Addon

### Installation

1. Open Anki
2. Go to Tools → Add-ons
3. Click "Get Add-ons"
4. Enter code: `1130800042`
5. Click OK and restart Anki

### Importing Templates

1. In Anki, go to Tools → Templates Import/Export
2. Click "Import"
3. Select this `catalog-export` directory
4. The addon will:
   - Read each subdirectory as a note type
   - Parse card files using the ` ``` ` delimiter to separate front/back
   - Apply `style.css` as the note type's CSS
   - Automatically append `common_epilog.html` to all templates
5. Confirm the import

### Exporting Templates

To export your modified templates:
1. Tools → Templates Import/Export → Export
2. Select a destination directory
3. Templates are saved in the same format as this directory

### Format Details

The addon recognizes:
- **Delimiter**: ` ``` ` (three backticks) separates front and back templates
- **CSS file**: `style.css` in each note type directory
- **Common epilog**: `common_epilog.html` appended to all templates
  automatically
- **Directory names**: Each subdirectory becomes a note type name

## Maintenance

When updating templates:
- Edit the source templates in `templates/default/`
- Rebuild using `tools/build.py` or update manually
- Recompile SCSS to CSS with: `sass --style=expanded --sourcemap=none
src/styles/scss:src/styles/css`
- Copy updated `src/styles/css/nord.css` to `catalog-export/style.css`

## Notes

- The hint toggle functionality only activates on card-type-2 (normal cards)
- All templates use Anki's template syntax: `{{Field}}`, `{{#Conditional}}`,
etc.
- The `common_epilog.html` must be appended to all templates for full
functionality
