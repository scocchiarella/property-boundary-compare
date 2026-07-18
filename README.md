# Property Boundary Compare

A self-contained, single-file tool for comparing two property lots at true relative scale. Open `index.html` in any browser — no build step, no dependencies. Live at **https://scocchiarella.github.io/property-boundary-compare/**.

Preloaded comparison: **9034 W Mallory Rd** (parcel 53-04-20-201-003.000-011, ≈3.10 ac) vs **5120 N Capitol Ave** (parcel 53-04-11-302-004.000-013, ≈1.01 ac), Bloomington, IN 47404 — both are the official surveyed boundaries from the IndianaMap statewide parcel layer (`Parcel_Boundaries_of_Indiana_Current`, fetched 2026-07-18).

## Features

- Overlay two lot boundaries at shared scale (feet), with per-lot opacity, show/hide, rotation, and drag-to-position
- **Satellite imagery toggle**: Esri World Imagery clipped to each lot's boundary, riding its rotation — when on, each lot's Opacity slider controls its imagery. Lets you compare what's actually *on* the land (wooded strips, clearings, outbuildings) at true relative scale
- **Per-lot north arrows**: each lot rotates independently, so each gets its own compass arrow showing which way true north points for that lot — useful for thinking about sun exposure / solar potential while shapes are rotated to align
- **Address lookup**: type any Indiana address and fetch its official parcel boundary live from the IndianaMap FeatureServer (attribute match first, geocode + point-intersect fallback via Nominatim). Needs internet; works from the hosted page or a local copy
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
- Geocoding fallback: OpenStreetMap Nominatim
