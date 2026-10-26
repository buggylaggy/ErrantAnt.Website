---
id: 20261025-vtile-port
status: completed
queue: current
depends_on: []
created: 2026-10-25
updated: 2026-10-25
---

# Goal

Refine the errantant.com homepage after the user's review ("pretty cool, but some refinement remain"): VUI.Web gets the
full VTile behavior of VUI.Avalonia (hover growth that pushes other tiles, press, flip and live faces, entrance, color
wash and picture backdrop), the top bar is painted like Errant Ant's bar, a super-wide tile in a group of its own rolls
through screenshots of the app and dyes the page background with the hovered screenshot like the app's start page, and
the page's tiles use the richer effects.

## Acceptance

- VUI.Web 0.2.0 reproduces VUI.Avalonia's VTile UI and motion with the app's metrics and springs: the hover edge, the
  emphasized tile growing ×1.16 from its top-left and pushing the tiles right of and below it (across groups when the
  growth exceeds the group gap), the press scale and tilt, the live flip (a roll about the horizontal axis, one tile at
  a time), the entrance turn with its stagger, the color wash of the hovered tile over the page and bar, and the
  picture backdrop.
- The top bar is painted like Errant Ant's: an opaque chrome surface showing the page's grid blurred where it lies under
  the bar, following the grid's shot; while a picture backdrop shows, the bar's paint fades so the backdrop shows
  behind the wordmark and links.
- A super-wide tile in a group of its own rolls through the four app screenshots. Hovering it stops the roll, grows it
  and pushes the other tiles like any VTile, fades the grid out, and fills the page and bar behind everything with the
  current screenshot, blurred and tinted like the app's start-page backdrop; leaving brings the grid back with a new
  shot.
- The page's existing tiles use the richer effects: live tiles with back faces flip in turn, every tile grows and
  pushes under the pointer, and the hovered tile washes the page in its color.
- Without script the page still shows every tile's front face; the site still makes no requests to other origins, has
  no console errors and no horizontal scroll from 375 px up.

## Design

### Facts: VUI.Avalonia and Errant Ant (read from code 2026-10-25; VUI = `VUI.Avalonia/src/VUI.Avalonia/`, EA = `ErrantAnt/src/ErrantAnt.App/`)

- Units: "px" tunables scale with the UI scale (EA: window height / 600); plain numbers (turnOffset 28, depth 800, blur
  90, copyBlur 6, grid spacing 140) do not.
- Springs (VUI `Motion/SpringValue.cs:47-99`, `motion.json:26-39`): unit mass, damping 2ζ√k, substeps ≤ 1/240 s,
  frame dt from rAF (0 or > 100 ms → 1/60 s), rest when |x−t| < ε and |v| < 10ε, retargeting keeps velocity. Presets
  (k, ζ → 2% / rest): settle 260, 0.9 → 0.30 / 0.47 s; pop 380, 0.72; hover 520, 0.85 → 0.20 / 0.42 s; exit 500, 1
  → 0.28 / 0.48 s; turn 70, 0.92 → 0.60 / 0.88 s; drift 4, 1; glide 1.5, 1 → 4.8 / 7.5 s. ε: hover, press, away,
  dive, picture 0.001; tilt 0.02°; flip 0.2°; rects 0.05 px; backdrop opacity 0.002; drift 0.05 px.
- Tile transform (`TileMotion.cs:173-234`): origin center; scale(grow·press·dive) → rotate3D(X, Y, perspective 800)
  → translate(centerShift + away·28, shiftY); opacity (1−|away|)(1−dive).
- Hover (`VTilePanel.cs:300-326`, `TileProjection.cs:14-77`): the hovered (else keyboard-focused) tile is emphasized
  with z-index 1; its edge (1.5 px `#FFFFFFB3` inside the clip) fades with a hover spring. Its rect becomes 1.16w ×
  1.16h anchored top-left; a tile wholly right of a moved tile's rest rect, overlapping its current vertical span,
  whose left edge the moved tile's right edge passes (minus the gap) shifts right by 0.16w; the same rule pushes down
  by 0.16h; pushes chain, each tile moves at most once per axis, no group check (a tile qualifying for both axes in
  one pass ends up moved down only). Rects animate on the hover spring.
- Press (`TileMotion.cs:68-82`, `VTile.cs:593-609`): point normalized −1..1 from center (keyboard 0, 0); ×0.96,
  tiltY = nx·10°, tiltX = −ny·10°, perspective 800, hover spring; release returns on hover spring.
- Live flip (`VTilePanel.cs:169, 408-439`): tiles with a back face; every 12 000 ms while visible: a tile showing its
  back flips back, else the next tile in child order flips (first flip lands on index 1); only one back at a time; the
  hovered or focused tile is skipped. Flip value 0↔180 on the turn spring, angleX = v ≤ 90 ? v : v − 180, faces swap
  at 90°; color, picture and scrims do not turn.
- Entrance (`VTilePanel.cs:329-406`, `TileMotion.cs:149-171`): tiles start at away −1 (−28 px X, rotateY −70°,
  opacity 0) and spring to 0 on settle, 40 ms × index; group headers fade in. Exit and the ×5 dive belong to page
  navigation, which this one-page site does not have.
- Tile layers (`VTile.axaml:23-118`): underlay (with tile blur: opaque canvas plus a 6 px blurred grid copy), color at
  tile opacity, picture, image scrim `#00000066` (opacity = picture fill), curtain `#000000B3`, head scrim
  `#00000066` over the top half when text contrast < 3, faces, edge, rings. Picture at rest fits (w−16)×(h−16)·0.45
  bottom-left with an 8 px inset; on hover it springs (settle) to cover ×1.12 and drifts toward a random direction by
  half the overflow on the drift spring; both return on settle.
- Start-page backdrop (EA `Shell/MainWindow.axaml:47-73`, VUI `VTileBackdrop.cs:94-172`): stack canvas → backdrop
  (strength 0.28, image strength 0.6) → grid → pages → bars. The hovered tile's color washes the page at 0.4·0.28 =
  0.112 (two layers cross-fading on settle) and the bar at 0.2. A picture tile's image springs to 0.51 with an
  acrylic: image cover, blur 90, margin −180 px, a 0.45 tint of the tile color, 128 px grain at 0.05; it drifts by the
  picture's direction × 60 px on glide. While a picture shows, the grid fades out and both bars' paint fades to 0 over
  400 ms; leaving for a tile without a picture or empty space fades the grid back with a new shot; moving between
  picture tiles keeps the grid hidden.
- Bar paint (EA `Shell/StatusBar/BarPaint.axaml:11-26`): opaque `canvas.subtle`, a copy of the paper grid blurred
  6 px drawing the same lines where they lie under the bar and following their fade and shot, the hovered tile's wash
  at 0.2, the section tint at 0.2 (the one-page site has no sections).
- Grid shot: starts from the current pose wrapped to angle mod 90 and pan mod one cell; constant line width at any
  zoom.

### Facts: this site and VUI.Web

- Screenshots (1606×1060 RGB PNG, aspect 1.515, macOS window captures) are saved under
  `.tasks/attachments/20261025-vtile-port/`: `start-page.png`, `library.png`, `vocabulary.png`, `map.png`; 1.0 MB
  together. No image converter is installed (no cwebp, avifenc, ImageMagick or Pillow).
- VUI.Web 0.1.0 (`/home/yuki/project/VUI.Web`, commit `da68943`): `Tile` (hover edge, CSS press scale and tilt, CSS
  entrance with nth-child steps), `TileGroup` (4 columns), `TopBar` (sticky, chrome at 72% with a backdrop blur, which
  hides the 10%-ink grid almost entirely), `GridBackdrop` (canvas grid with one shot). No growth, push, flip, live
  faces, wash or backdrop. The site pins `packages/vui-web-0.1.0.tgz`; changes ship as 0.2.0.
- A 6×4 tile is 348×230 units, aspect 1.51, so a screenshot fills it uncropped.

### Design

- VUI.Web 0.2.0 adds one panel engine (a script owned by the package, attached to the `.vui-groups` container): the
  spring integrator above, per-tile hover, press, flip, away and rect springs, the projection rule for growth and push
  over the tiles' measured rest rects (cross-group, as in the app), the live round-robin timer, the entrance, and the
  wash and picture backdrop layers. It runs a rAF loop only while a spring moves and re-measures rest rects on resize.
  `Tile` gains an optional `back` slot (a live tile) and a `picture` (the app's picture layer: fit bottom-left at rest,
  cover ×1.12 with drift on hover, image scrim, backdrop on hover); a rolling picture tile takes several pictures,
  each with a caption, and its own interval, and rolls between them with the flip motion while not hovered.
- Without script every tile shows its front face; an inline head script marks the document so tiles start hidden
  only when the engine will bring them in (no flash before the entrance).
- `GridBackdrop` exposes its pose so the bar draws a 6 px blurred copy of the same lines; `TopBar` gets the opaque
  chrome paint, the wash at 0.2 and the 400 ms fade while a picture backdrop shows.
- The super tile: a 6×4 tile alone in the first group, before Coming soon, so the screenshots fill it uncropped. Its
  faces are the four screenshots, each with a small label; it rolls with the flip motion; hovering stops the roll and
  shows the current screenshot as the backdrop.
- Layout: the site sets `--vui-cell: clamp(72px, 5.45vw, 120px)` so the 6-column group, Coming soon and Reading
  (952 units with gaps and padding) share the first row from about 1440 px wide; narrower windows wrap. The body
  clips horizontal overflow (`overflow-x: clip`, which keeps the sticky bar working) so tiles pushed past the window
  edge are cut like in the app instead of adding a horizontal scrollbar.
- Screenshots go to `public/screenshots/` as WebP (Pillow in a throwaway virtualenv, full 1606×1060, quality about
  90), with the PNGs kept out of the site.
- Back faces (live tiles, all true per the ErrantAnt README):
  - Hero: label "No ratings to keep", text "Errant Ant counts how often you meet each word and phrase and how often
    you look it up, and colors words by how well you know them."
  - 12 UI languages: "From English and Deutsch to 日本語 and 繁體中文".
  - Local-first: "Works offline: the English dictionary ships with the app".
  - EPUB · PDF · TXT: "Text-based PDFs are rebuilt into reflowing text".
  - Paged reading: "Turn pages with →, Space or a click in the margin".
  - Word panel: "Keep for reading makes a group of words a unit of its own".
  - Knowability: the app's "LAST LOOKUP" face, "possession", "Pride and Prejudice".
  - Map: the app's count face, "5" "linked terms" (the drawing's five words).
  - Vocabulary · Phrases: "Search, filter by language, sort by least known".
  - Import word lists: "Import them as words or as phrases".
  - The reader sample and Coming soon keep one face.
- Super tile: group title "A look inside"; captions "Start page", "Library", "Vocabulary", "Map" in a small chip of
  the curtain color (`#000000B3`) bottom-left, so the screenshots are not darkened by a head scrim; it rolls every
  5 s; each picture's backdrop tint and wash use the screenshot's bar color (sampled from the image: paper, rose,
  teal, lavender).

## Scope

Included: VUI.Web (version 0.2.0, packed and pinned) and this site, including the four screenshots under `public/`.

Excluded: page navigation, exit and dive motion, section tints, editing, long-press actions and drag reordering (no
use on a one-page site); the Buggy Laggy restyle (its own task); commits and pushes unless asked.

## Execution slices

- [x] Tracer: the VUI.Web panel engine with the spring integrator, hover edge, growth and push over measured rest
  rects, on the site's current tiles (package source synced into `node_modules` while developing). Proves that
  measured CSS-grid rects across separate groups can drive the app's projection rule with springs, without layout
  thrash or scroll overflow; flip, entrance, bar and backdrop come after.
- [x] Press tilt and entrance on springs, the `back` slot and live round-robin flip; no-script fallback.
- [x] Bar paint (opaque chrome, blurred grid copy following the shot), page and bar wash, picture layer and picture
  backdrop with the grid fade and re-shot.
- [x] Super tile: rolling picture tile, WebP screenshots, the "A look inside" group, layout cell and overflow clip.
- [x] Back faces on the site's tiles; VUI.Web README; pack 0.2.0, pin it, remove the 0.1.0 tarball.

## Current state

Completed 2026-10-25. VUI.Web 0.2.0 (`/home/yuki/project/VUI.Web`, uncommitted) adds `TileWall`, `PictureRoll` and
`PageBackdrop` (replacing `GridBackdrop`) and the engine modules `src/engine/motion.ts`, `grid.ts`, `backdrop.ts`,
`panel.ts`; it is packed and pinned here as `packages/vui-web-0.2.0.tgz` (0.1.0 removed). The site has the "A look
inside" group, back faces, the cell override and WebP screenshots in `public/screenshots/`. Nothing is committed or
pushed in either repository. The original PNGs stay under `.tasks/attachments/20261025-vtile-port/`.

Changes made during execution (recorded under Decisions): tiles became opaque (no backdrop blur), the picture
backdrop's blur is drawn into a canvas at an eighth of the size with the tint folded in, and `Tile` got no single
`picture` at rest because no tile uses one.

## Validation

Built-in browser pane, shown (a hidden pane throttles frames), against `npm run preview`:
- Package and build: `npm ci` and `npm run build` pass with `vui-web-0.2.0.tgz` pinned; the lockfile names 0.2.0
  only; `node_modules/vui-web/src` equals VUI.Web's `src`.
- Growth and push: after a real hover settles, the tile's rect is 1.16× its rest size from the same top-left, a tile
  to its right is shifted by 0.16 of its width, a tile below by 0.16 of its height; hovering the super tile shifts the
  Coming soon group; leaving returns every rect to rest.
- Press: a real pointerdown sets the tilt from the pointer position.
- Live flip: within two intervals one tile shows its back face and no other does; a hovered tile is skipped.
- Roll: the super tile's face advances on its interval and holds while hovered.
- Backdrop: hovering the super tile raises the backdrop layer with the current screenshot, fades the grid canvas
  and the bar paint; leaving restores both and the grid's pixels change (a new shot).
- Bar: the bar's grid copy is drawn, blurred 6 px, and changes with the main grid during the shot.
- Wash: hovering a colored tile tints the page layer with that color near 0.112.
- No-script: the built HTML shows front faces, and back faces are hidden by the stylesheet alone.
- Phone 375×812 and desktop 1440×900: `scrollTo(200, 0)` leaves `scrollX` 0, also while a tile is hovered; no face
  (front or back) overflows its tile; one cropped capture each, and one during the super tile's backdrop.
- Requests and console: same-origin only; no console errors.

Evidence (2026-10-25). The built-in browser pane delivered no animation frames (the session window was not open: 0
rAF frames per second), so motion was checked in headless Chrome 156 (`/usr/bin/google-chrome --headless=new`)
driven over the DevTools protocol with trusted mouse and keyboard input, against `npm run preview`:

- Package and build: `npm ci` then `npm run build` built 1 page with `vui-web-0.2.0.tgz` pinned; the lockfile names
  0.2.0 only; `node_modules/vui-web/src` equals VUI.Web's `src`; `dist/` holds `CNAME`, the favicon and the four WebP
  screenshots (251 KB together, from 1.0 MB of PNG).
- Entrance: 3 s after load no tile keeps a transform or opacity (all at rest).
- Growth and push (1440×900, hovering the 341×166 hero): it grows ×1.158 after 0.9 s from its top-left; Coming soon,
  12 and Local-first move down 26.2–26.5 px (0.16 × 166 = 26.6), and the Reading group's tiles move right 54.2–54.9 px
  (0.16 × 341 = 54.6), across the group gap as in the app; leaving returns every tile to rest. While the super tile
  is hovered `scrollTo(300, 0)` leaves `scrollX` 0 (tiles pushed past the edge are clipped).
- Wash: hovering the slate hero puts `rgb(62, 74, 92)` at 0.112 on the page and 0.2 in the bar; the hover edge
  reaches 1.
- Press: a held press near the top-right corner of the hovered Coming soon tile gives `rotateX(8.3°) rotateY(5.5°)`
  and scale 1.1136 (1.16 × 0.96); release returns to 1.16; the click still raises the notice. Tab onto the tile
  emphasizes it (×1.16).
- Live flip: over 26 s one tile shows its back at a time (12 UI languages, the second live tile, as in the app; then
  back; then Local-first), and none flips while hovered.
- Roll: the super tile shows its second picture after 5 s and keeps it through 7 s of hover; hovered, the picture is
  at ×1.12 with a drift.
- Backdrop: hovering the super tile raises the picture layer to 0.51 with a 225×158 canvas holding the blurred, tinted
  screenshot; the grid canvas and its bar copy lose all ink and the bar paint goes to 0; 1.5 s after leaving, the
  grid and copy are drawn again, the bar paint is 1, and once settled the grid's pixels differ from before the hover
  (a new shot) and then stay still.
- Bar: the copy canvas has `filter: blur(6px)`, holds ink, and its pixels change during the opening shot.
- No-script: with scripts disabled every tile shows exactly its front face (the roll its first picture) and none is
  hidden.
- Layout: at 1440×900 the screenshot group (515×341, aspect 1.51), Coming soon and Reading share the first row; at
  375×812 the screenshot tile is 4×3 (343×256), cells are 81.25 px; at both sizes `scrollTo(300, 0)` leaves
  `scrollX` 0 and no face (front or back) overflows its tile.
- Requests and errors: only `http://localhost:4321` requests; no exceptions or console errors in any run.
- Look: captures at 1440 (rest, and hovering the super tile) and 375 judged: the bar shows the grid softly blurred like
  the app's, the hover fills page and bar with the blurred, tinted screenshot.
- Frame rate (headless software rendering on a host at load 7–9 on 4 cores, so only relative): at rest about 50–60
  fps; hovering a plain tile 40–53 fps after the changes below (15 before); hovering the super tile about 20–30 fps
  (3 before).

## Decisions

- 2026-10-25: The user asked for these refinements after reviewing the live homepage, and supplied the four app
  screenshots for the rolling tile.
- 2026-10-25: The super tile is 6×4 cells, alone in the first group before Coming soon (screenshot shape, uncropped).
- 2026-10-25: The user delegated the remaining details. Delegated choices, each reversible:
  - (delegated) Live tiles flip on the app's 12 s round-robin, one at a time; alternative: a shorter web-only
    interval.
  - (delegated) The super tile rolls every 5 s with the flip motion and holds while hovered; alternative: a
    cross-fade.
  - (delegated) Back-face texts as listed under Design, all taken from the ErrantAnt README; alternative: no back
    faces on informational tiles.
  - (delegated) Group title "A look inside"; screenshot captions in a dark chip instead of the app's head scrim, so
    the pictures stay bright; alternative: the head scrim.
  - (delegated) Backdrop tint and wash per screenshot from its own bar color; alternative: one fixed tint.
  - (delegated) Tiles keep the web's backdrop blur instead of the app's opaque underlay with a per-tile grid copy, so
    15% of what lies behind (grid, wash, backdrop) shows through; alternative: a grid copy per tile.
  - (delegated) The site's cell `clamp(72px, 5.45vw, 120px)` and `overflow-x: clip` on the body; alternative: the
    package default cell, which puts only two groups in the first row.
  - (delegated) Screenshots as WebP; alternative: the PNGs as supplied (1.0 MB).
- 2026-10-25 (delegated, during execution): Tiles are their color at 85% over an opaque canvas instead of a live
  backdrop blur, reversing the earlier choice: the blur on 14 moving tiles held hover at about 15 fps in software
  rendering, and Errant Ant's tile underlay is itself an opaque canvas with a faint blurred grid, so this is closer to
  the app; the wash and picture backdrop now show only around the tiles, as in the app. Alternative: the backdrop blur.
- 2026-10-25 (delegated, during execution): The picture backdrop's 90 px blur and tint are drawn once into a canvas at
  an eighth of the size and stretched, instead of a CSS blur on a window-sized image (3 fps); the grain stays a
  separate layer as in the app (costing a few fps in software rendering). Alternative: a CSS filter.
- 2026-10-25 (delegated, during execution): `Tile` has no single `picture` with the app's small rest layout, because no
  tile on either site uses one; `PictureRoll` covers picture tiles. Alternative: port the rest layout now.
