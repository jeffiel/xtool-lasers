# xTool Laser Selector

A single-page web app for looking up which xTool laser machine and laser type to use for a given material and operation (cutting or engraving).

## Usage

Open `index.html` directly in a browser — no build step, no server required.

1. Select a **material** from the first dropdown
2. Select an **operation** (Cutting or Engraving)
3. Matching machines are shown as cards with laser type, wavelength, and power

## Data source

Machine/material data is pulled live at page load from this Google Sheet:
https://docs.google.com/spreadsheets/d/1D8IwqrZlZ952c4S_OgaL-f1SaiIxrfPofkOPQOaPZac

The sheet has one row per machine/laser-type combination with columns:

| Column | Description |
|--------|-------------|
| Model | Machine name (e.g. F1 Ultra) |
| Laser Type | CO2, IR (Fiber), Blue Diode, MOPA (Fiber), UV |
| Wavelength (nm) | Laser wavelength as a number |
| Power | Output power (e.g. 20W) |
| Cutting | Comma-separated list of cuttable materials |
| Engraving | Comma-separated list of engravable materials |

The header shows a green **● Live** badge when data loaded successfully, or a yellow **Cached data** badge if the network request failed and the built-in fallback data was used instead.

## How it works

**Data fetching — JSONP**

The Google Visualization API (`gviz/tq`) endpoint does not send CORS headers, so `fetch()` would be blocked by the browser. The app uses JSONP instead: it injects a `<script>` tag pointing at the endpoint with `tqx=responseHandler:<callbackName>`, which calls a temporary global function with the parsed JSON. This works from any origin including `file://`.

**Material parsing**

Each Cutting/Engraving cell is a comma-separated string. Some materials contain commas inside parentheses (e.g. `Plastics (PE, PP, EVA, PET, etc.)`), so the parser tracks parenthesis depth and only splits on commas at depth 0. Material names are then normalized by title-casing any lowercase word-starts while leaving existing uppercase intact (preserving `MDF`, `EVA`, etc.).

**Rendering**

The material dropdown is populated dynamically from the union of all cutting and engraving materials found in the loaded data, sorted alphabetically. Results are rendered as cards color-coded by laser type:

| Laser type | Color |
|------------|-------|
| CO2 | Red |
| IR (Fiber) | Amber |
| Blue Diode | Blue |
| MOPA (Fiber) | Violet |
| UV | Purple |

## Files

```
index.html   — everything: markup, styles, and script in one file
```
