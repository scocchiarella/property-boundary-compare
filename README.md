# Property Boundary Compare

A self-contained, single-file tool for comparing two property lots at true relative scale. Open `index.html` in any browser — no build step, no dependencies. Live at **https://scocchiarella.github.io/property-boundary-compare/**.

Preloaded comparison: **9034 W Mallory Rd** (parcel 53-04-20-201-003.000-011, ≈3.10 ac) vs **5120 N Capitol Ave** (parcel 53-04-11-302-004.000-013, ≈1.01 ac), Bloomington, IN 47404 — both are the official surveyed boundaries from the IndianaMap statewide parcel layer (`Parcel_Boundaries_of_Indiana_Current`, fetched 2026-07-18).

## Features

- Overlay two lot boundaries at shared scale (feet), with per-lot opacity, show/hide, rotation, and drag-to-position
- **Lot fill switcher — Color / Satellite / Street map**: imagery is clipped to each lot's boundary and rides its rotation; whichever fill is active, each lot's Opacity slider controls it. Satellite (Esri World Imagery) shows what's actually *on* the land (wooded strips, clearings, outbuildings); Street map shows road context and **building outlines** (OpenStreetMap by default — set `GOOGLE_KEY` in `index.html` to a referrer-restricted [Map Tiles API](https://developers.google.com/maps/documentation/tile) key to use Google roadmap tiles instead, which have the most complete house footprints)
- **Per-lot north arrows**: each lot rotates independently, so each gets its own compass arrow showing which way true north points for that lot — useful for thinking about sun exposure / solar potential while shapes are rotated to align
- **Address lookup**: type any Indiana address and fetch its official parcel boundary live from the IndianaMap FeatureServer (attribute match first, geocode + point-intersect fallback via Nominatim). Needs internet; works from the hosted page or a local copy
- **Compare table with privacy score**: side-by-side attributes for both lots — auto-computed (lot area, perimeter, house footprint, nearest neighboring roof, count of other-parcel structures within 500/1000/1500/2500 ft, and apartments/commercial within half a mile, all from live OpenStreetMap building footprints via Overpass) plus manual listing fields (beds, baths, finished sqft, year built, price) that persist in the browser per property name
- **Woods vs open estimate**: in-browser texture classification of the satellite tiles (local std-dev via integral images), clipped to the parcel — reports wooded and open acreage per lot. Winter imagery reads conservative; leaf-on canopy is somewhat higher
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
