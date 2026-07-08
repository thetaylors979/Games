# HIKE TRACKER — Mobile Web Prototype Spec

## Concept & Vision
A field-ready GPS hike tracker that lets hikers import a photo of a trail map, anchor it to real GPS coordinates, then overlay their actual path on top. It's like having a topo map and a pencil, but better. Works offline once loaded. Designed for real use on actual hikes — big tap targets, high contrast, readable in sunlight.

## Design Language
- **Aesthetic:** Outdoor field gear — rugged but clean. Think Garmin Montana meets a well-designed government USGS app.
- **Colors:** 
  - Background: #1a1a1a (near-black for battery savings)
  - Surface: #2a2a2a (card backgrounds)
  - Primary: #4ade80 (bright green — active/recording)
  - Accent: #f59e0b (amber — warnings, elevation)
  - Text: #f0f0f0 (off-white)
  - Muted: #888888 (secondary text)
- **Typography:** System UI stack for performance — no external fonts
- **Spatial system:** Large touch targets (min 48px), generous padding, readable at arm's length
- **Motion:** Minimal — just enough to confirm actions. No flourishes that waste battery.
- **Visual assets:** Inline SVG icons only, no external assets

## Layout & Structure
- **Single-page app** — one scrollable view
- **Phases (tabs/steps):**
  1. **Map Import** — load a trail map photo
  2. **Geo-Reference** — pin 2-3 known points
  3. **Track** — start recording, see live stats
  4. **Review** — see final overlay, stats, export
- **Bottom-fixed action bar** — always shows the primary action button
- **Top status bar** — GPS signal strength, elapsed time, distance
- **Mobile-first** — works in portrait, thumb-reachable controls

## Features & Interactions

## Features & Interactions

### 1. Map Import + Trail Auto-Lookup
The Map tab offers two paths to set up a hike:

#### Trail Auto-Lookup (Primary Path)
- User enters **Park name** (e.g. "Guadalupe Mountains NP") and/or **Trail name** (e.g. "Bush Mountain Trail")
- "Find Trail" queries the **OpenStreetMap Overpass API** (free, no API key)
- Overpass searches for relations/ways matching name + hiking route tags within USA bounds
- Trail geometry is extracted: nodes, waypoints, trailhead, trail end
- Trail type is auto-detected: **out-and-back**, **point-to-point**, or **loop**
- For **loops**: waypoints at ~25% intervals serve as reference markers
- For **out-and-back**: trailhead + midpoint + 3/4-point markers used
- Map auto-aligns to trail coordinates — no manual tapping needed
- A **Trail Info Card** shows: trail type, trailhead coords, trail end coords, waypoints count, alignment method
- "Find Trails Near Me" button: geolocates user, searches 5km radius for named trails, shows sorted results list to tap and select
- **If trail not found**: shows friendly error, falls back to raw GPS tracking (no map reference)
- All trail data comes from OpenStreetMap — requires network at lookup time only

#### Manual Map Import (Fallback Path)
- Tap "Import Trail Map" → file picker for image (camera roll)
- Or "Take Photo" → camera
- Image displays full-screen with aspect-fit
- Shows dimensions once loaded
- "Use This Map" confirms → advances to Geo-Reference tab

### 2. Geo-Reference
Two modes depending on how the hike was set up:

#### Trail Auto-Lookup Mode
- Shows trail path preview on the Geo-Ref canvas
- Trailhead (TH), trail end, and waypoint markers displayed
- No manual control points needed — trail data is the reference
- Can switch directly to Track tab

#### Manual Map Mode (original)
- Shows the imported map full-screen
- Tap "Add Control Point" → tap a known landmark on the photo
- Modal asks for real-world GPS (lat/lon) OR lets user enter estimated coordinates
- 2-3 points minimum, more = better alignment
- "Align Map" computes perspective transform and warps the image to real coords
- Uses Canvas 2D `setTransform()` or manual quad-warp math

### 3. GPS Tracking
- Tap big green "START HIKE" button
- Browser requests geolocation (high accuracy)
- **With trail reference**: trail path drawn as amber dashed reference; live GPS track drawn in green on top; auto-fits to trail bounds; trailhead (TH) and end markers shown; waypoints shown
- **With map reference**: warped map image as background, GPS track overlaid
- **With no reference**: auto-fits to GPS track bounds
- Live stats: distance (mi/km), elapsed time, elevation gain/loss
- Tap red "STOP" to end
- GPS indicator shows signal strength

### 4. Review & Export
- Final overlay: trail reference (dashed) + GPS track line (solid green)
- Stats summary card
- "Save to Photos" — exports canvas as PNG
- "New Hike" resets

## Component Inventory

### ImportButton
- Large dashed border box, centered icon + text
- States: empty (dashed), hover (green border), loaded (thumbnail preview + filename)

### ControlPointPin
- Circular marker with number (1, 2, 3...)
- Tap to place, tap again to edit
- Linked to GPS coordinate input row

### GPSStatusBar
- Signal icon (bars), accuracy in meters
- Time elapsed (HH:MM:SS)
- Distance (X.XX mi)

### ActionButton
- Full-width, 56px tall
- Start: green background, "START HIKE"
- Stop: red background, "STOP HIKE"  
- States: default, active (pulsing glow), disabled (greyed)

### StatsCard
- Dark surface, 3-column grid: Distance | Time | Elevation
- Numbers large, labels small muted

### GeoRefModal
- Dark overlay, centered card
- Lat/lon inputs with number keyboard
- "Use My Current GPS" button to auto-fill from phone

## Technical Approach
- **Single HTML file** — self-contained, no external dependencies
- **Geolocation API** — `navigator.geolocation.watchPosition` with high accuracy
- **Canvas 2D** — all rendering: map image, warped overlay, track polyline, trail reference
- **Perspective warp** — manual 4-point transform (bilinear or perspective via triangulated mesh) for manual map mode
- **OpenStreetMap Overpass API** — `https://overpass-api.de/api/interpreter` for trail lookup (requires network at lookup time)
- **Trail geometry extraction** — relations and ways resolved to node coordinates; ways stitched via endpoint proximity matching
- **Trail type detection** — loop (start/end within 50m), otherwise out-and-back or point-to-point
- **localStorage** — persists current hike session and last-imported map as base64
- **File API** — image import from camera roll or camera capture
- **No frameworks** — vanilla JS, minimal DOM manipulation

## Geo-Reference Math
Given 2-3 (photo_x, photo_y) → (gps_lat, gps_lon) pairs, compute affine transform:
- With 2 points: scale + rotation + translation
- With 3+ points: least-squares best fit
- Apply transform to all photo tap points to get real-world coordinates
- When rendering track points, invert transform to go (gps → photo pixel) for overlay
- Perspective warp via 4-corner mapping if 4 control points available

## Offline Capability
- Once loaded, works completely offline
- Map image stored as base64 in localStorage
- Track points accumulated in memory, saved to localStorage on stop
- Trail auto-lookup requires network at lookup time only — after lookup, trail geometry is stored in memory and the trail reference works offline
- No network requests whatsoever after initial load and trail lookup
