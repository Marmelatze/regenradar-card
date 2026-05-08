# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Development (watch mode — rebuilds on file change)
npm run watch

# Production build (output to dist/card.js)
npm run build
```

No test suite exists. There is no lint script.

## Architecture

This is a **Home Assistant Lovelace custom card** distributed via HACS. It displays an animated precipitation radar map using data from the [Brightsky API](https://brightsky.dev/) (DWD radar data).

**Entry point:** `src/index.ts` — registers two custom elements with the browser and announces the card to Home Assistant's custom card registry.

**Two custom elements:**

- `regenradar-card` (`src/card.ts`) — outer HA Lovelace card shell built with LitElement. Receives `lat`/`lon`/`zoom` from card config and passes them into the map element.
- `regenradar-card-map` (`src/map.ts`) — the heavy-lifting element (extends `ReactiveElement`, not `LitElement`). Manages an OpenLayers map with OSM tiles, a home-location marker, and a precipitation radar image layer. Fetches radar frames from Brightsky every 15 minutes.
- `regenradar-card-editor` (`src/editor.ts`) — simple form rendered in the HA card editor UI for setting `lat`/`lon`.

**Radar rendering pipeline (`src/map.ts`):**
1. Fetch JSON from `https://api.brightsky.dev/radar` (returns frames of base64-zlib-encoded 16-bit precipitation grids).
2. `decompress()` — base64 decode + pako `inflate` → `Uint16Array`.
3. `precipitation_to_rgba()` — maps values (0–250, unit: 1/100 mm per 5 min) to RGBA using the turbo colormap (`src/js-colormap.ts`), with no-rain rendered fully transparent.
4. `makeSource()` — draws each frame onto a `<canvas>`, converts to a data URL, wraps in an OpenLayers `ImageStatic` source.
5. `FrameControl` (custom OL `Control`) — slider + play/pause toggle that cycles through frames at 500 ms intervals.

**Coordinate system:** Radar grid uses the DWD stereographic projection (`DE1200`), registered at runtime via proj4. All coordinate transforms between EPSG:4326 (WGS84), EPSG:3857 (OL default), and DE1200 go through `ol/proj`.

**Build:** Parcel 2 bundles everything (including OL CSS via `bundle-text:`) into a single `dist/card.js`. The `parcel-namer-hashless` plugin ensures the output filename has no content hash (required for HACS, which expects a stable `card.js`).
