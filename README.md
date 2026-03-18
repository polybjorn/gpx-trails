# GPX Trails

Single-page GPX route viewer with interactive maps, elevation profiles, and route stats.

## Features

- Route index grouped by region and country
- Overview map with all routes
- Per-route detail view with map, elevation profile, and stats (distance, gain, loss, peak, estimated time)
- Multi-track GPX support with per-track colors, collapsible elevation charts, and track legend
- Dual-track convention: `Route.gpx` (recorded) + `Route.planned.gpx` (planned reference) shown as dashed overlay
- Completion status derived from file existence -no config needed
- Dark theme, responsive layout

## File structure

```
gpx/
  Region/
    Route Name.gpx              # recorded track (= completed)
    Route Name.planned.gpx      # planned reference route
metadata.json                   # optional source URLs for routes
gpx-manifest.sh                 # generates routes.json from gpx/ directory
index.html                      # the app
favicon.svg                     # mountain silhouette icon (custom, no attribution needed)
```

## Adding a route

1. Drop the `.gpx` file into `gpx/Region/`
   - Recorded track: `Route Name.gpx`
   - Planned route: `Route Name.planned.gpx`
   - Both can exist for the same route
2. Optionally add a `source` URL in `metadata.json`
3. Run `./gpx-manifest.sh` to regenerate `routes.json`

## Multi-track GPX

A single `.gpx` file can contain multiple `<trk>` elements (stages, alternatives). Each track renders with a distinct color, its own elevation chart, and per-track stats in the legend.

## Setup

Served as a static site. No build step, no dependencies.

The manifest script scans `gpx/` for `.gpx` and `.planned.gpx` files, detects elevation data and multi-track info, reads metadata, and generates `routes.json`.

```sh
./gpx-manifest.sh

# serve locally
python3 -m http.server 8080
```

## Stack and attribution

- [Leaflet](https://leafletjs.com/) (BSD-2-Clause) -maps
- [leaflet-gpx](https://github.com/mpetazzoni/leaflet-gpx) (BSD-2-Clause) -GPX rendering
- [leaflet-elevation](https://github.com/Raruto/leaflet-elevation) (GPL-3.0) -elevation profiles

Tile providers:
- [OpenTopoMap](https://opentopomap.org) -CC-BY-SA
- [CyclOSM](https://www.cyclosm.org) -ODbL (OpenStreetMap)
- [Esri World Imagery](https://www.esri.com) -Esri
- [CARTO](https://carto.com) -CC-BY 3.0
- [OpenStreetMap](https://www.openstreetmap.org) -ODbL

## GPX data

GPX files are excluded from this repository. To use this app, add your own GPX files to `gpx/Region/` and run `./gpx-manifest.sh`.

## Privacy

GPX files can contain PII -timestamps, device identifiers, and start/end coordinates that may reveal home locations. A pre-commit hook is included at `.git/hooks/pre-commit` that automatically strips `<time>`, `<author>`, and `creator` attributes from any staged `.gpx` files. Since git hooks don't transfer with clones, set it up manually after cloning:

```sh
cp hooks/pre-commit .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

---

Personal project built with [Claude Code](https://claude.ai/claude-code).
