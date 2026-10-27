---
id: 20261026-live-banner
status: completed
queue: current
depends_on: [20261026-plain-copy]
created: 2026-10-26
updated: 2026-10-26
---

# Goal

Turn the home page's banner from the Steam library art (a picture) into real VTiles at the art's exact places, and
polish the rest: no "A look inside" title, the screenshot tile keeps its curtain under the pointer, a Map tile drawn
like the art's map tiles, and the Errant Ant wordmark art in the bar instead of the text.

## Acceptance

- The banner shows no picture: the art's 31 tiles are real tiles on the page's own backdrop, at the art's cells and
  sizes in the art's five groups (4, 4, 5, 4 and 4 cells wide, 7 rows), centered and cropped like the art was, with
  the art's drawings on them (icons, map, progress cards, books, text bars) and the logo with its sparkles on the big
  blue tile.
- A banner tile under the pointer grows and pushes the tiles right of and below it, as on the rest of the wall; a
  click on one scrolls to the screenshot tile. The banner's tiles turn in and away with the pivot like the others.
- The screenshot group has no title; the screenshot tile's dark curtain stays while it is emphasized.
- The Map tile is drawn like the art's map: white word cards and lavender note pills joined by thin white links.
- The bar's first item shows the wordmark from `Errant Ant.vcanvas` (the app's title art), standing on the same
  baseline as the small items to its right, and the bar keeps its height between the pages.
- Build is clean; no overflow at 375 px; no console errors.

## Design

### Facts (verified 2026-10-26)

- The art is a VUI tile wall at cell 150 px (u = 150/53): gap 17, group gap 119, groups 4/4/5/4/4 cells wide starting
  at x −28.7 (the wall is centered: 3897.5 px wide in 3840), rows at y 44 + 167 n, 7 rows (44 px margins).
- Tiles (group: col,row cols×rows color art):
  A: 0,0 2×2 violet map · 2,0 slate plus · 3,0 olive settings · 2,1 2×2 red progress · 1,2 steel list.
  B: 0,0 4×2 white reader text · 0,2 2×2 blue book (red cover) · 2,2 2×2 teal progress · 3,4 red bookshelf.
  C: 0,0 teal language · 4,0 violet grid · 0,2 5×4 blue logo · row 6: teal language, violet grid, red bookshelf,
  steel list, olive settings.
  D: 0,0 2×2 teal progress · 2,0 2×2 violet map · 0,2 4×3 navy book (green cover) · 0,5 2×2 white notes ·
  2,5 olive settings · 3,5 steel list · 2,6 2×1 red progress.
  E: 0,0 4×2 blue book (navy cover) · 0,2 red bookshelf · 1,2 slate plus · 2,2 2×3 white column text ·
  0,3 2×2 violet map · 0,5 2×1 teal progress · 2,5 ochre language.
- The art's icons are the app's uxwing icons (`ErrantAnt.App/Resources/Assets/Icons`): grid_view, plus_line,
  settings_line, list_view, bookshelf, language; at (30, 26) in a 50 px box.
- `Errant Ant.vcanvas` is the same drawing as the app's `tools/art/errant-ant-title.vcanvas`.
- vui-web's pivot turns only the first `.vui-groups` of a page, and the panel measures tiles with offsetLeft/Top, so
  the banner tiles must be in HomeWall's TileWall with no positioned ancestor below it.

### Changes

- `public/brand/wordmark.svg` (from the vcanvas) and `public/brand/icons.svg` (a sprite of the six icons);
  `public/brand/hero.webp` goes.
- `Banner.astro`: the art's groups as `.vui-group-tiles` grids in a full-bleed box, each tile a vui-web `Tile` linking
  to `#look-inside`, placed by `grid-area` in its style (which replaces Tile's own style, so it carries the color).
  The banner redefines the VUI metrics from the art's cell (cover of the banner box), so the drawings, gaps and
  corners scale with it.
- `TileArt.astro`: each tile's drawing as SVG in the art's pixels.
- `HomeWall.astro`: the banner first inside the TileWall; the screenshot group untitled; the Map tile redrawn.
- `SitePage.astro`: no separate banner. `site.css`: banner, map, curtain, empty group header, wordmark.

## Decisions

- 2026-10-26: The user asked for: no "A look inside" text; the curtain kept on hover; the Map tile reworked like the
  art's; the wordmark from `Errant Ant.vcanvas` in the bar, aligned with the items on its right; the art's tiles as
  real VTiles at the same places, pushing on hover and scrolling to the screenshot tile on click.
- 2026-10-26 (delegated): The banner tiles carry the art's drawings (redrawn as SVG), not plain color, so the banner
  keeps the art's look; the alternative was plain colored tiles. The off-tile sparkles of the art go with the
  picture; the four on the big blue tile stay around the logo.
- 2026-10-26 (delegated): The tiles keep VUI's corners and tile opacity like the rest of the wall, rather than the
  art's square, fully opaque ones.
- 2026-10-26 (delegated): The Map tile shows real words on the art's white cards (word plus a gray meaning line),
  not the art's bars.

## Validation

- `npm run build` is clean.
- Browser at 1440×900: banner tile rects match the art's cells scaled to the banner; hovering one tile moves its
  right and lower neighbours (transforms read back); a click lands the screenshot group under the bar; the curtain's
  opacity is 1 while the screenshot tile is emphasized; the bar height is the same on both pages and the wordmark's
  bottom is on the small items' baseline; one cropped screenshot of the banner and of the Map tile.
- 375 px: `scrollWidth` equals the width.

## Current state

Completed 2026-10-26, not committed (the user did not ask this time). Evidence from the browser at 1440×900:
31 banner tiles on 56.25 px cells (the art's 150 at 1440 / 3840), the wall centered with its first tile at −18 px;
hovering the plus tile scaled it 1.158 and moved the olive tile right and the red tile below down by 8.8 px while the
steel tile stayed; a click on the logo tile scrolled to `#look-inside` with its top at 108 px under the 86 px bar;
the curtain's opacity was 1 with the screenshot tile emphasized; the wordmark item kept the 48.5 px line, the bar was
86 px on both pages, and the pivot turned the banner tiles away and back. At 375 px the scroll width was 375 and
the big tile centered. No console errors. The sprite's first build carried the icons' `<?xml?>` declarations, which
broke every icon after the first; the declarations are stripped now and the file parses.
