---
id: 20261103-store-tiles
status: completed
queue: current
depends_on: []
created: 2026-11-03
updated: 2026-11-03
---

# Goal

The Steam tile opens Errant Ant's Steam store page, and the Microsoft Store and Steam tiles move up into the banner in
place of two of its tiles.

## Acceptance

- The Steam tile is a link to https://store.steampowered.com/app/5507590 that opens in a new tab, like the Microsoft
  Store tile; clicking it no longer raises the "coming soon to Steam" bar, and that bar is gone from the page.
- In the banner's second group, left of the logo, the Microsoft Store tile takes the blue book's 2×2 cell (0,2) and
  the Steam tile the teal 69% ring's (2,2); neither is in the "Get it" group any more. Every other banner tile keeps
  its art and cell.
- In the banner the store tiles keep their own faces: Microsoft Store blue with the mascot, title "Microsoft Store",
  caption "Windows"; Steam slate, title "Steam", caption "Coming soon"; both at the banner's tile scale.
- Build is clean; no console errors.

## Design

### Facts (verified 2026-11-03)

- `src/components/HomeWall.astro`, group "Get it": the 4×2 about text tile, then the Microsoft Store 2×2 tile (an
  external link with `target="_blank" rel="noopener"`), then `<ComingSoonTile />`, then Languages and Local-first.
  Groups are 4 cells wide and fill row by row, so today the about text takes the group's first two rows and the two
  store tiles share the next two.
- `src/components/ComingSoonTile.astro` holds the Steam tile (a tile-button, slate, title "Steam", caption "Coming
  soon"), the notice bar and the script that shows it; `src/styles/site.css` styles `.notice` / `.notice-shown`.
- The Steam page at that URL is titled "Errant Ant on Steam", is a coming-soon page with a wishlist button and gives
  the release date as Nov 9, 2026.
- `README.md` describes "a Steam coming-soon button".
- `src/components/Banner.astro` builds the banner from five groups of `BannerTile`s (cell, size, color, drawing); each
  renders as a `Tile` with `href="#look-inside"` and a `TileArt` drawing, its grid area set inline. `.banner
  .vui-tile-face { padding: 0 }` lets the drawings cover the tiles. Its 2×2 tiles: group 1 the violet map (0,0) and the
  red ring (2,1); group 2, left of the logo, the blue book (0,2) and the teal ring (2,2) side by side; group 4, right of
  the logo, the teal ring (0,0) and the violet map (2,0) side by side. The logo group (3) has only 1×1 tiles besides
  the logo.
- A banner cell is 150/3840 of the window's width with a 280 px minimum banner height (56 px at 1440 wide, 34 px on a
  phone, where the wall's cell is 72-120 px). Below about 870 px wide the banner crops its outer groups: on a 375 px
  phone about two columns of groups 2 and 4 next to the logo group stay in view.

### Changes

- The Steam tile becomes a link, `href="https://store.steampowered.com/app/5507590" target="_blank" rel="noopener"`,
  keeping its slate color, title and "Coming soon" caption (the store page still says coming soon).
- Both store tiles leave `HomeWall.astro`'s "Get it" group (about text, Languages, Local-first remain) and become
  entries of the banner's group 2 in `Banner.astro` at (0,2) and (2,2), replacing the blue book and the teal ring.
  A banner entry is then either a drawing or a store link (title, caption, href, the mascot for Microsoft Store), so
  one list keeps every cell; a store entry renders its `Tile` with `href`, `target="_blank" rel="noopener"`, its title
  and caption and no `TileArt`.
- The face padding reset `.banner .vui-tile-face { padding: 0 }` narrows to the drawn tiles, so the store tiles keep
  VUI's face padding for their title and caption.
- Remove `ComingSoonTile.astro` and the `.notice` styles, which only served the button, and the red book cover in
  `TileArt.astro`, which only the replaced blue book used.
- `README.md`: "a Steam coming-soon button" becomes the banner's Microsoft Store and Steam links.

## Decisions

- 2026-11-03: The user asked for the Steam tile to link to https://store.steampowered.com/app/5507590 and for the
  Microsoft Store and Steam tiles to move to the top.
- 2026-11-03: "Top" means the banner: the two store tiles replace two of the banner's tiles.
- 2026-11-03: The user picked the two 2×2 tiles left of the logo (group 2's blue book and teal ring), Microsoft Store
  on the blue one; the alternatives were group 4's ring and map right of the logo, or group 1's map and ring. On a
  phone only the Steam cell stays in view.

## Validation

- `npm run build` is clean; `dist/index.html` has the Steam link and no `.notice` bar.
- Browser at about 1440×900: the two store tiles' boxes sit in group 2's bottom row beside the logo group (measured
  rects), their `href`s and `target` are right, the "Get it" group has no store tiles, and a cropped screenshot of the
  banner shows legible faces; once at 375 px wide, the Steam tile is in view. No console errors.

## Current state

Completed 2026-11-03; not committed. Evidence from the final worktree:

- `npm run build` clean (both pages). `dist/index.html` has the Steam tile as
  `<a href="https://store.steampowered.com/app/5507590" target="_blank" rel="noopener">` at grid area 3 / 3, the
  Microsoft Store one at 3 / 1, and no `notice` or `coming-soon` markup (nor in `dist/privacy/index.html`).
- Browser at 1440×900 (built site via `astro preview`): group 2's layout boxes are the reader page (0,17 244×119),
  Microsoft Store and Steam side by side at y 142, 119×119 each, x 271 and 396, then the bookshelf 1×1; the logo group
  starts at x 559. Both links open in `_blank` with `noopener`; titles 11.7 px with VUI's face padding, the Microsoft
  Store tile with its mascot; drawn tiles keep padding 0. The "Get it" group holds the about text, Languages and
  Local-first. The pane was hidden, so the turn-in springs did not run; for the cropped screenshot the tiles were
  pinned flat by an injected style, which showed both faces in place left of the logo.
- At 375×812 the Steam tile spans x −3…68 (3 px under the left edge), the Microsoft Store tile −79…−7 (out of view),
  as decided; no horizontal page scroll (scrollWidth 375). No console errors at either size.
