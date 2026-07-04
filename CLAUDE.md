# CLAUDE.md, Fishing Chart App

## What this project is
A single self contained web app (index.html) for planning fishing trips out of Shelter Island and around Montauk and Block Island. It is a Leaflet map with fishing spots, a boat route from Coecles Harbor, distance and run time tools, and live NASA satellite overlays for chlorophyll and sea surface temperature. It is meant to run in a real browser (desktop or mobile Safari or Chrome) and to be added to an iPhone home screen as an app.

Everything lives in one file, index.html. There is no build step. Editing the file and refreshing the browser is the whole workflow. When deployed to a static host (GitHub Pages, Netlify, Cloudflare Pages, Vercel) the file must be named index.html so it serves as the homepage.

## Hard requirement: it needs the internet
The map cannot work offline. It loads three things live over https: the Leaflet library from a CDN, the base map tiles (OpenStreetMap), and the NASA GIBS satellite overlays. A local HTML file opened through the iPhone Files app will NOT work, because iOS opens it in a sandboxed preview with no internet. That is the entire reason this is being hosted at a URL. Any host that serves the file over https will work.

## Boat and launch points
- Home port: Coecles Harbor, Shelter Island. Entrance approx 41.0780 N, 72.3000 W.
- Second reference: Montauk Point Light, 41.0708 N, 71.8572 W.
- Boat: Regulator 26 center console. Default cruise speed 30 mph (editable in the app).

## Distance and routing conventions (do not change without asking)
- All distances are shown in BOTH nautical miles and statute miles. Water distances are computed in nautical miles (great circle) and multiplied by 1.15078 for statute miles.
- Bearings are degrees TRUE, not magnetic. Local variation is about 13 degrees west if a magnetic course is ever needed.
- Runs from Coecles do NOT use a straight line, because Gardiners Island sits in the way. They follow a traced 12 waypoint corridor: out of Coecles, east across Gardiners Bay, through the gap south of Gardiners Island, northeast across Napeague Bay, along the Montauk north shore past Culloden and Shagwong, and around Montauk Point.
- Routing rule in code: spots north of Montauk (latitude 41.04 or higher, the Block Island Sound spots) exit the corridor at waypoint W11 and go direct, skipping the point rounding. Ocean spots run the full corridor through W12 (rounding Montauk Point) then go direct.
- Reference figures for sanity checks: Coecles to Montauk Point is about 20 NM (23 statute miles). Coecles to Tuna Ridge South of Block is about 50 NM by the route. These are correct; the straight line looks shorter only because it cuts across land.

## The 12 corridor waypoints (Coecles departure)
W1 Coecles Hbr 41.0780, -72.3000
W2 Harbor exit 41.0730, -72.2820
W3 Gardiners Bay 41.0690, -72.2020
W4 Approaching Gardiners 41.0570, -72.1410
W5 Turning south 41.0430, -72.1350
W6 S. Gardiners gap 41.0290, -72.1200
W7 Gap east 41.0320, -72.0900
W8 Napeague Bay NE 41.0520, -72.0580
W9 Off north shore 41.0920, -71.9840
W10 Off Culloden Pt 41.0950, -71.9400
W11 Off Shagwong/Mtk inlet 41.0890, -71.8800
W12 Round Montauk Pt 41.0750, -71.8350

## Spot data conventions
- Source coordinates came from a loran and GPS spot sheet, in degrees and decimal minutes (for example 40-55.000 means 40 degrees 55.000 minutes). The app stores decimal degrees.
- 63 spots have GPS positions and are mapped. Nine more had only loran TD numbers and are not mapped.
- Two source typos were corrected: Dump NW Side latitude (printed 40-19.52, corrected to 40-49.52 to match the other Dump corners) and Mountain East of Coxes longitude (printed 74-06.300, corrected to 71-06.300, since 74 W is inland New Jersey).
- Spots are split into two groups: inshore / Block Island (blue) and offshore / canyons (red).

## Satellite overlays (NASA GIBS)
- Chlorophyll is a stack of three satellite layers shown together so swath gaps and cloud holes in one are filled by the others: OCI_PACE_Chlorophyll_a, VIIRS_NOAA21_Chlorophyll_a, VIIRS_NOAA20_Chlorophyll_a. (Originally a single MODIS_Aqua_Chlorophyll_A layer, but NASA retired MODIS Aqua and its feed stopped publishing, so it was replaced July 2026.) Temp layer: GHRSST_L4_MUR_Sea_Surface_Temperature. Tile matrix GoogleMapsCompatible_Level7 (coarse, a few km per pixel, good for spotting breaks, not fine detail).
- The temp legend is labeled in degrees F using NASA's fixed color scale, which spans 0 to 32 C (32 to 90 F) end to end.
- Tap-for-temp: tapping open water (with Measure off) queries NOAA ERDDAP (coastwatch.pfeg.noaa.gov, dataset jplMURSST41, the same MUR dataset as the overlay) for the exact surface temp at that point on the selected Sat date, shown in a popup in F and C. Taps on markers, lines, and land are ignored or answered with a friendly message. The request uses JSONP (a script tag), not fetch, because NOAA's server does not send CORS headers and browsers block plain fetch to it.
- Chlorophyll also draws the previous day's three satellite layers underneath the selected day's, so cloud holes show the most recent clear view instead of blank map (six tile layers total when the button is on).
- These are for finding color and temperature breaks where bait and tuna hold, especially near the offshore spots.
- The overlays are date stamped and lag a couple of days, and single days can be cloud patchy. The app defaults the date to two days back and has a date picker. If a layer looks blank, roll the date back 2 to 4 days.
- If NASA is down, the base map still works; only the overlays fail.

## Features currently in the app
- Spot search box, and a route from Coecles box that draws the real route with distance and run time.
- Measure tool (tap two points for distance, true bearing, run time).
- Range rings at 10, 20, 30, 40 NM from Coecles and from Montauk Point.
- Toggle layers: inshore spots, offshore spots, both range ring sets, the route corridor, and base map choice.
- A status bar at the bottom that reports whether the map engine and tiles loaded, so failures are visible instead of blank.
- iOS web app meta tags so Add to Home Screen launches full screen.

## Bathymetry and fronts (added July 2026)
- Base map choices now include the real NOAA electronic nautical chart (WMS at gis.charttools.noaa.gov, MCS/ENCOnline, layers 0-7) with soundings and contours, and GEBCO shaded bathymetry (wms.gebco.net) which shows the shelf break and canyons. Both are WMS layers, not tile pyramids.
- Fronts button: client side edge detection on the GIBS imagery itself (canvas, crossOrigin anonymous, GIBS sends CORS). SST mosaic threshold 35 no blur; chlorophyll stack mosaic gets a 3x3 alpha-aware box blur then threshold 60; both edge maps are despeckled (keep pixel only with 2+ of 8 neighbors). Red = temp break, green = color break, purple = both within 2 px (~1 km at z6). Rendered as an L.imageOverlay for the current view at min(zoom,6); user rescans manually after panning. Gradients are never computed across transparent (no data) pixels so cloud boundaries do not read as fronts.
- Sat date now auto-detects: on load the app probes GIBS for the newest date with a published SST tile (up to 6 days back) and sets the date picker. This fixed the recurring "overlays are blank" complaint which was publish lag, not an outage.

## Custom spots and calculator (added July 2026)
- "+ Spot" button: tap the chart, name it (and optional depth), and it becomes a purple marker. Custom spots join the search list, the route box, and the calculator. Popup has rename and delete links.
- Custom spots persist in localStorage per phone. The app also tries to sync them through a shared JSON bin (extendsclass.com bin fbacbcc) but that service rejects browser writes (its CORS preflight returns 500), so cross-phone sync is NOT working yet. The plan is to swap the BIN constant to a Firebase Realtime Database URL once the owner does the one-time Google setup. The pull/push/self-heal logic is already written to survive an empty or lost bin (local copy re-uploads).
- Distance calculator panel (from the owner's Excel workbook): pick any two spots or launches, get NM, statute miles, direct true bearing, and run time at the panel cruise speed. If either end is Coecles Harbor the distance follows the 12 waypoint corridor, same as the route box. Draws a dashed purple line on the chart.
- The Excel workbook also has a 6 waypoint corridor version on its Ref sheet. The app keeps the more detailed 12 waypoint corridor for both the route box and the calculator.

## Ideas for future work (ask before big changes)
- A multi stop trip planner (click spots in order for total mileage and time).
- Tag spots with typical tuna season notes.
- Save favorite spots or recent routes.
- Optional: a fully offline base layer (embedded coastline vectors) so the core chart renders with zero external servers. The satellite overlays would still need the internet.

## Style note
In any written content or drafts for this owner, do not use em dashes or en dashes. Use commas, periods, parentheses, or separate sentences.
