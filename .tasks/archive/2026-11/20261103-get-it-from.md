---
id: 20261103-get-it-from
status: completed
queue: current
depends_on: [20261103-store-logos]
created: 2026-11-03
updated: 2026-11-03
---

# Goal

Both banner store tiles say "Get it from …" with their store's name, beside the stores' marks.

## Acceptance

- The Microsoft Store tile's caption reads "Get it from Microsoft Store" in place of "Windows", and the Steam tile
  gets the caption "Get it from Steam": the small caption at the tile's bottom, as "Windows" is today.
- The captions don't touch the marks at 1440 px or on a 375 px phone.
- The marks stay as they are: centered, unaltered, with clear space between them and the text.
- Build is clean; no console errors.

## Design

### Facts (verified 2026-11-03)

- `src/components/Banner.astro`: a `Store` entry is href, logo and an optional caption; the Microsoft Store tile has
  the caption "Windows", the Steam tile none. `Tile` takes a `title` (11 u, about 11.7 px at 1440 px wide) and a
  `caption` (7 u, about 7.4 px), both at the bottom of the face; the face's body is about 100 px wide on a 119 px
  tile at 1440 px and 52 px on a 375 px phone.
- The badge already reads "Download from the Microsoft Store"; Microsoft asks for it not to be crowded with other text
  or graphics, and Valve for its logo to stand alone with clear space and not be combined with words
  (20261103-store-logos).
- The badge ends 35.5 px above the "Windows" caption at 1440 px; the Steam logo has 45 px free below it.

### Changes

- `Banner.astro`: the two store entries' captions become "Get it from Microsoft Store" and "Get it from Steam"; with
  every store tile captioned, `Store.caption` turns back from optional to required. The marks, their centering rule
  and the tiles' links stay as they are (the logo centers in the body above the caption, so the Steam logo moves up
  the way the badge already sits).

## Decisions

- 2026-11-03: The user asked for "get it from xxx" on both store tiles.
- 2026-11-03: The user chose the small caption on both tiles, "Get it from Microsoft Store" replacing "Windows" and
  "Get it from Steam", over a larger title line that kept "Windows".

## Execution slices

1. The two captions and the required `caption` in `Banner.astro`.

## Validation

- `npm run build` clean; `dist/index.html`'s store links carry the two captions and "Windows" is gone from them.
- Browser, built site with the tiles pinned flat: at 1440×900 and once at 375 px wide, each caption's box starts
  below its mark's box (measured rects) and stays inside its tile; no console errors.

## Current state

Completed 2026-11-03; not committed. Evidence from the final worktree:

- `npm run build` clean; `dist/index.html`'s store captions read "Get it from Microsoft Store" and "Get it from
  Steam", and no tile says "Windows".
- Browser, built site, tiles pinned flat (the hidden pane runs no springs): at 1440×900 both captions are one line of
  7.4 px inside their 118.9 px tiles, 35.5 px below the badge and 34.1 px below the Steam logo; at 375×812 both stay
  one line inside 71.5 px tiles, 21.4 and 20.5 px below the marks. A cropped screenshot at 1440 shows both captions
  under their marks. Every request of this task's loads was to `localhost:4321` with 200 or 304; the tab's console
  held four `ERR_CONNECTION_REFUSED` errors from the screenshot requests of a load cut off when the previous task
  stopped the preview server, and none since.
