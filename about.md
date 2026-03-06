# Chromatic — Color Palette Studio

> **This project — including the entire `color-palette.html` application and this README — was generated using [Claude AI](https://claude.ai) by Anthropic.**

---

A single-file, zero-dependency HTML color palette editor for designing, previewing, and exporting a sequence of 9 colors. Open `color-palette.html` directly in any modern browser — no build step or server required.

---

## Features

### Color Input

Each of the 9 colors can be edited through three fully synchronized input methods:

- **HEX** — type any 6-digit hex code (e.g. `#DC143C`). 3-digit shorthand is also accepted.
- **RGB** — individual numeric fields for Red, Green, and Blue channels (0–255).
- **HSL Sliders** — drag sliders for Hue (0–360°), Saturation (0–100%), and Lightness (0–100%). Slider track gradients update dynamically to reflect the current color context.

All three input modes are bidirectional — changing any one instantly updates the other two. The Hue slider is preserved even on achromatic (gray) colors, so raising Saturation after adjusting Hue produces the expected tinted result.

### Palette Overview Strip

A horizontal strip at the top displays all 9 colors side by side. Each cell shows the nearest CSS color name in both **white** and **black** text simultaneously, so legibility on that background is immediately apparent. Hovering a cell expands it and reveals its hex code. Clicking a cell copies the hex code to the clipboard and shows a confirmation toast.

### Context Previews (Canvas)

Two canvas panels render the palette as thin horizontal lines — one on a **dark background** (`#111111`) and one on a **light background** (`#F5F5F2`). Between each line, the color's sequence index, HEX, RGB, and HSL values are displayed, showing how the colors read in context next to each other.

### Drag-to-Reorder

Each color row has a ⠿ **drag handle** on its left edge. Grabbing the handle and dragging reorders the palette sequence. A glowing line indicator shows exactly where the color will be inserted — above or below each row — enabling precise placement between any two existing colors. The palette strip and canvas previews update instantly on drop.

### Color Names

Color names are resolved by nearest-neighbor RGB distance against a built-in library of ~100 standard CSS/web color names (e.g. "Crimson", "Steel Blue", "Dark Goldenrod"). Names are shown in the palette strip and used as comments in the Pine Script export.

### Pine Script Export

The **Export Pine Script** button opens a modal with syntax-highlighted Pine Script v5 code containing:

- **Individual variables** — one `var color_N = #RRGGBB` declaration per color (index starts at 0), with an inline comment showing the sequence index and nearest color name.
- **Color array** — a `var palette = array.from(...)` block listing all hex values with aligned inline comments.

The modal includes a **Copy to Clipboard** button that strips syntax highlighting and copies clean, paste-ready Pine Script text, confirmed with a ✓ flash.

### Reset

The **Reset** button in the header restores all 9 colors and their sequence to the default palette. The icon rotates on hover as a visual cue.

---

## Usage

1. Open `color-palette.html` in any modern browser (Chrome, Firefox, Safari, Edge).
2. Edit colors using the HEX field, RGB inputs, or HSL sliders in any row.
3. Drag rows by their ⠿ handle to reorder the sequence.
4. Click any swatch in the **Palette Overview** strip to copy its hex code.
5. Click **Export Pine Script** to generate and copy color variables for use in TradingView Pine Script indicators or strategies.
6. Click **Reset** to restore the default palette at any time.

---

## Technical Notes

### Architecture

The entire application is a single self-contained HTML file with no external dependencies beyond Google Fonts (loaded from CDN for typography only). All logic is plain vanilla JavaScript; no frameworks or libraries are used.

### Color Conversion

All conversions between HEX, RGB, and HSL are implemented from scratch:

- `rgbToHex` / `hexToRgb` — bidirectional hex↔RGB conversion with 3-digit hex expansion.
- `rgbToHsl` / `hslToRgb` — standard HSL conversion using the min/max lightness algorithm.

### Hue Preservation

A `storedHue[]` array tracks the last meaningful hue value per color row. When Saturation is 0 (achromatic), the Hue slider position and value are preserved rather than snapping to 0, allowing hue to be set before increasing saturation.

### Drag and Drop

Reordering uses the **Pointer Events API** (`pointerdown`, `pointermove`, `pointerup`) rather than the HTML5 Drag and Drop API. This approach provides smooth, lag-free dragging, a floating ghost clone of the dragged row, and a precise between-row drop indicator line.

### Canvas Rendering

The context preview canvases are drawn with the HTML5 Canvas 2D API and re-render on every color change and window resize. Each stripe is 4px tall with per-color labels rendered in the gap between stripes.

### Pine Script Output

The export generates Pine Script v5 compatible syntax. Hex color literals are used directly (e.g. `#DC143C`) as supported natively in Pine Script v5. The `array.from()` constructor is used for the palette array. All inline comments are column-aligned for readability.

---

## Default Palette

| Index | Default Color         |
|-------|-----------------------|
| 0     | Red-Orange `#DC503C`  |
| 1     | Orange `#EB9632`      |
| 2     | Yellow `#F5D246`      |
| 3     | Green `#5ABE5A`       |
| 4     | Sky Blue `#32A0C8`    |
| 5     | Blue `#5064D2`        |
| 6     | Purple `#9646D2`      |
| 7     | Pink `#D24646`        |
| 8     | Gray `#828282`        |

---

## Browser Compatibility

Requires a modern browser with support for:

- CSS custom properties and `backdrop-filter`
- HTML5 Canvas 2D API with `roundRect`
- Pointer Events API
- `navigator.clipboard.writeText`

Tested on Chrome 120+, Firefox 121+, Safari 17+.
