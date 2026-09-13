# Property Boundary Compare

A self-contained, single-file tool for comparing two property lots at true relative scale. Open `index.html` in any browser — no build step, no dependencies. Live at **https://scocchiarella.github.io/property-boundary-compare/**.

Preloaded comparison, three lots at shared scale: **7243 N Red Hill Rd**, Ellettsville 47429 (parcel 53-03-33-400-017.000-001, ≈2.48 ac) · **5120 N Capitol Ave**, Bloomington 47404 (53-04-11-302-004.000-013, ≈1.01 ac) · **9034 W Mallory Rd**, Bloomington 47404 (53-04-20-201-003.000-011, ≈3.10 ac) — all official surveyed boundaries from the IndianaMap statewide parcel layer (`Parcel_Boundaries_of_Indiana_Current`; Red Hill fetched 2026-09-13, the others 2026-07-18).

The tool takes **any number of lots**, not just two: **+ Add lot** appends one (150 × 150 ft placeholder, six colors in rotation), **Remove lot** drops one, and Address lookup / paste-import each target a chosen lot via a picker. Header, stat chips, compare table, stack and side-by-side all follow the current lot list.

## Features

- Overlay two lot boundaries at shared scale (feet), with per-lot opacity, show/hide, rotation, and drag-to-position
- **Lot fill switcher — Color / Satellite / Street map**: imagery is clipped to each lot's boundary and rides its rotation; whichever fill is active, each lot's Opacity slider controls it. Satellite (Esri World Imagery) shows what's actually *on* the land (wooded strips, clearings, outbuildings); Street map shows road context and **building outlines** (OpenStreetMap by default — set `GOOGLE_KEY` in `index.html` to a referrer-restricted [Map Tiles API](https://developers.google.com/maps/documentation/tile) key to use Google roadmap tiles instead, which have the most complete house footprints)
- **Per-lot north arrows**: each lot rotates independently, so each gets its own compass arrow showing which way true north points for that lot — useful for thinking about sun exposure / solar potential while shapes are rotated to align
- **Address lookup**: type any Indiana address and fetch its official parcel boundary live from the IndianaMap FeatureServer (attribute match first, geocode + point-intersect fallback via Nominatim). Needs internet; works from the hosted page or a local copy
- **Compare table with privacy score**: side-by-side attributes for both lots — auto-computed (lot area, perimeter, house footprint, nearest neighboring roof, count of other-parcel structures within 500/1000/1500/2500 ft, and apartments/commercial within half a mile, all from live OpenStreetMap building footprints via Overpass) plus manual listing fields (beds, baths, finished sqft, year built, price) that persist in the browser per property name
- **Woods vs open estimate**: in-browser texture classification of the satellite tiles (local std-dev via integral images), clipped to the parcel — reports wooded and open acreage per lot. Winter imagery reads conservative; leaf-on canopy is somewhat higher
- **Woods/open tint layer**: the classification rendered as a green/yellow overlay on the canvas, so contiguous woodland reads visually — across your boundary when the surrounding square is on
- **Surrounding area square**: a same-sized square of neighborhood context (default 10 acres, adjustable) rendered behind each lot from the same imagery, dimmed outside the parcel so the lot pops. Rotates and moves with the lot — answers "does this property's woods touch a bigger woods?" at true shared scale. Needs satellite or street fill
- **Privacy rings overlay**: 500/1000/1500/2500 ft rings around each house with neighbor building footprints drawn on the canvas (red within 1000 ft, orange within 2500 ft), from the same Overpass data as the Compare table
- **Roads and noise**: nearest road and nearest major road (motorway/trunk/primary/secondary/tertiary within 2 mi) with name and distance from the house, plus a rough road-noise estimate (per-class source level at 50 ft, −4.5 dB per distance doubling) — a screening heuristic, not a measurement
- Paste-import fallback: paste GeoJSON, ArcGIS JSON (`rings`), or raw lat/lon pairs. WGS84 and Web Mercator are auto-projected to local feet
- Overlap reads visually via blend modes (multiply in light theme, screen in dark)
- Scale bar, grid, per-edge length labels, live area/perimeter and size-ratio readouts
- Stack, side-by-side, and zoom-to-fit layout helpers; pan/zoom with mouse, wheel, or pinch
- Shape editing: drag corners, double-click an edge to add a corner, double-click a corner to remove it, or set a W×D rectangle

## Keyboard

With a lot selected (click it): arrow keys nudge 1 ft (Shift = 10 ft), `[` / `]` rotate 1° (Shift = 15°).

## Data sources

- Parcel boundaries: [IndianaMap](https://www.indianamap.org/) `Parcel_Boundaries_of_Indiana_Current` FeatureServer (gisdata.in.gov)
- Satellite imagery: Esri World Imagery tile service
- Street map: OpenStreetMap standard tiles, or Google Map Tiles API (roadmap) when `GOOGLE_KEY` is set
- Neighbor-roof counts: OpenStreetMap building footprints via the Overpass API (overpass-api.de with mirror fallbacks)
- Geocoding fallback: OpenStreetMap Nominatim

## Optional: Google street tiles

OSM already shows building outlines for most of Bloomington, but Google's roadmap tiles have the most complete house footprints. To switch the street layer to Google:

```sh
gcloud auth login
gcloud services enable tile.googleapis.com --project YOUR_PROJECT   # needs billing enabled
gcloud services api-keys create --display-name="property-boundary-compare" \
  --api-target=service=tile.googleapis.com \
  --allowed-referrers="https://scocchiarella.github.io/*" \
  --project YOUR_PROJECT
```

Put the returned `keyString` in the `GOOGLE_KEY` constant near the top of the `<script>` in `index.html`. The referrer restriction makes the key safe to commit — it only works from the Pages site.
