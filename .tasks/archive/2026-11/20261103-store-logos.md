---
id: 20261103-store-logos
status: completed
queue: current
depends_on: [20261103-store-tiles]
created: 2026-11-03
updated: 2026-11-03
---

# Goal

The banner's store tiles carry their stores' logos: the Steam tile gets the Steam logo, and the Microsoft Store tile
shows the Microsoft Store logo instead of Errant Ant's mascot.

## Acceptance

- The Steam tile shows Valve's white (inverse) Steam logo, symbol and "STEAM" logotype together, unaltered in shape,
  centered with clear space around it, and no text: its title "Steam" and its caption "Coming soon" both go.
- The Microsoft Store tile shows Microsoft's app badge (it reads "Download from the Microsoft Store"), unaltered and at its own aspect ratio,
  centered with clear space; its caption "Windows" stays and its title "Microsoft Store" and the mascot go.
- Both links still open their store pages in a new tab.
- Build is clean; no console errors.

## Design

### Facts (verified 2026-11-03)

- `src/components/Banner.astro`: the store tiles are entries of the banner's group 2 (`Store`: title, caption, href,
  `mascot?`), Microsoft Store blue at (0,2) with `/brand/mascot.webp` as `.tile-mascot`, Steam slate at (2,2) with
  only its title "Steam" and caption "Coming soon". 2×2 banner tiles are 119 px square at 1440 px wide, 71 px on a
  375 px phone.
- `.tile-mascot` (`src/styles/site.css`) centers the image at 104% of the tile's height; only the Microsoft Store tile
  uses it.
- No Steam or Microsoft Store logo file exists in this repository, in `ErrantAnt/Assets/` (Errant Ant's own store
  art only) or elsewhere under `~/project`.
- Microsoft (https://apps.microsoft.com/badge, read 2026-11-03): the sanctioned mark for linking to a Store listing is
  the app badge (the current en-us one reads "Download from the Microsoft Store"), as a static image or a web component. Static images are hosted at
  `https://get.microsoft.com/images/<lang>%20<dark|light>.svg` (en-us: 161×44 SVG, ~26 KB). Rules: keep standard
  spacing, don't crowd it with text or graphics, don't stretch, recolor, crop or modify it; scaling with the aspect
  ratio kept is allowed. Microsoft-hosted files update with future rebranding. The page offers no standalone Store
  icon (the shopping bag) for third-party use.
- Valve (https://partner.steamgames.com/doc/marketing/branding and its Brand Guidelines PDF, pages 5-8): for games
  the Steam logo is the Steam symbol plus the "steam" logotype with ®, black on light backgrounds or white (inverse)
  on dark ones; no physical alterations, not combined with other branding, words or graphics; clear space of the
  locomotive crank's diameter around it; at least 50 px tall on screen (the ® may go when too small to read). Valve
  may ask for any use to be changed and invites artwork for approval at steambranding@valvesoftware.com. Official
  files: `steam_brandAssets.eps` on Valve's CDN (EPS; no converter is installed here). Valve's store header serves
  the same logo as SVG (`store.akamai.steamstatic.com/public/shared/images/header/logo_steam.svg`, 293×89, filled
  #C5C3C0).
- Fit: a 2×2 banner tile's face is about 100 px wide inside VUI's padding at 1440 px (52 px on a phone). The Steam
  logo there is about 100×30 px, under Valve's 50 px minimum height, which needs about 165 px of width; the Microsoft
  badge is about 100×27 px.
- The site makes no third-party requests today (earlier tasks verified only `localhost` requests); hot-linking
  `get.microsoft.com` would make visitors' browsers contact Microsoft.
- Valve's header SVG declares `viewBox="0 0 293.44px 89.333"`; the `px` makes the viewBox invalid, so an `<img>`
  smaller than 293×89 would clip the drawing instead of scaling it. Its six paths share one fill.
- `/brand/mascot.webp` is used only by the Microsoft Store tile (`.tile-mascot`); the link preview is `social.png`.

### Changes

- `public/brand/microsoft-store-badge.svg`: Microsoft's `https://get.microsoft.com/images/en-us%20dark.svg` (the
  generator's default dark badge), byte for byte.
- `public/brand/steam-logo.svg`: Valve's `logo_steam.svg` from its store header with two edits that leave the drawing
  as Valve made it: the viewBox without `px`, and its fill #C5C3C0 set to the guidelines' inverse white #FFFFFF.
- `Banner.astro`: a `Store` entry becomes href, logo (its file, width, height and alt text: the
  badge's words "Download from the Microsoft Store", and "Steam") and an optional caption ("Windows" on the Microsoft Store tile, none on Steam's), with no title
  or mascot; the store `Tile` renders the logo as one centered `<img>` and keeps any caption, `target="_blank"` and
  `rel="noopener"`. The logos' alt text names the links now that the titles go.
- `site.css`: `.tile-mascot` gives way to a store logo rule: the image centered in the tile at about three quarters
  of its width, height from its aspect ratio, which leaves the caption and the clear space both stores ask for
  (Valve's crank diameter is under a third of the logo's height).
- Remove `public/brand/mascot.webp`, which nothing else uses, and update `README.md`'s list of `public/brand/` files
  with the two store marks and where they come from.

## Decisions

- 2026-11-03: The user asked for the Steam logo on the Steam tile and the Microsoft Store logo on the Microsoft Store
  tile in place of the product logo (the mascot).
- 2026-11-03: The user chose to copy both SVGs into `public/brand/` rather than hot-link them, keeping the site free
  of third-party requests; that is the go-ahead to download those two files during execution.
- 2026-11-03: The user chose the stores' official marks (Microsoft's badge, Valve's white Steam logo) over bare icon
  marks, accepting that the Steam logo sits under Valve's 50 px minimum at banner scale. The chosen layout keeps the
  captions at the bottom and drops the titles, which the marks already name.
- 2026-11-03: The user dropped "Coming soon" from the Steam tile, which keeps no caption.

## Execution slices

1. Download the two SVGs into `public/brand/` and apply the Steam file's two edits; remove `mascot.webp`.
2. `Banner.astro` store entries and tile markup, the logo rule in `site.css`, `README.md`.

## Validation

- Files: the badge's SHA-256 equals the fetched original's; `diff` between Valve's original and `steam-logo.svg` shows
  only the viewBox and the fill.
- `npm run build` clean; `dist/index.html`'s store links carry the two logo images, the Microsoft Store tile its
  "Windows" caption, no "Coming soon" anywhere, no store titles and no mascot.
- Browser at 1440×900 (built site): each logo's rendered box has its file's aspect ratio, sits inside its tile
  centered horizontally and above the caption without touching it, and a cropped screenshot of the two tiles shows
  the marks whole; network requests only to `localhost`; no console errors.

## Current state

Completed 2026-11-03; not committed. Evidence from the final worktree:

- Files: `public/brand/microsoft-store-badge.svg` has SHA-256 `0e0cd666…fa69`, the file fetched from
  `get.microsoft.com/images/en-us%20dark.svg` (paths only, no script or fonts). `steam-logo.svg` differs from
  Valve's `logo_steam.svg` only in the viewBox (`px` dropped) and its nine fills (#C5C3C0 to #FFFFFF); no script.
  `public/brand/mascot.webp` is removed; nothing referenced it but the old tile.
- `npm run build` clean. `dist/index.html`: the Microsoft Store link holds the badge image (alt "Download from the
  Microsoft Store") and the caption "Windows"; the Steam link holds only the Steam logo (alt "Steam"); neither has a
  title; "Coming soon" appears nowhere.
- Browser at 1440×900 (built site, tiles pinned flat by an injected style since the hidden pane runs no springs):
  both tiles 118.9 px; badge 89.8×24.5 (ratio 3.665 vs the file's 3.659), Steam logo 89.8×27.3 (3.289 vs 3.292),
  each centered within 0.1 px, 14.5 px from the tile's sides; the badge ends 35.5 px above "Windows". The first
  measurement found the logos at 76 px because the percentage resolves against the tile body inside VUI's padding;
  the width went from 76% to 90% of the body and was measured again. A cropped screenshot shows the badge and the
  white Steam logo whole on their tiles. Requests only to `localhost:4321`; no console errors.
- During validation the badge turned out to read "Download from the Microsoft Store", not "Get it from Microsoft";
  the alt text and README say so.
