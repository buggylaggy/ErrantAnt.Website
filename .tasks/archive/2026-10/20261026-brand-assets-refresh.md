---
id: 20261026-brand-assets-refresh
status: completed
queue: current
depends_on: []
created: 2026-10-26
updated: 2026-10-26
---

# Goal

Refresh the whole site around the new brand assets in `/home/yuki/project/ErrantAnt/Assets`: the library hero art
(which the user likes most) as the home page's banner with the Errant Ant logo on it, the art's cream and colors for
the page and tiles, the real app icon as favicon and touch icon, a social preview card, and the store page's facts in
the tiles. Then commit, push to `main` and let GitHub Pages publish it.

## Acceptance

- The home page opens on a full-bleed banner of `library_hero_3840x1240.png` under the bar, with
  `library_logo_853x720.png` standing on the art's big blue center tile; the banner keeps the art's shape on wide
  windows, is cropped around its center (never squashed) on narrow and very wide ones, fades into the page at its
  bottom, and leaves with the home page when the pivot moves to the privacy page and returns with it.
- The page canvas, bar, accent and tile colors come from the art (cream `#fff7ec`, blue `#2f6ac3`, and the art's tile
  colors), so the banner meets the page without a visible color step.
- The browser tab shows the app icon (favicon.ico with 16/32/48 px), iOS uses the app icon as touch icon, and the old
  four-square `favicon.svg` is gone.
- Link previews show `main_capsule_1232x706.png` with the page's title and description (Open Graph and Twitter card).
- The tiles state what the store page says today: Early Access on Windows and Linux with macOS to follow, 12 learning
  languages and 24 offline dictionaries, interface in 12 languages, what comes next in Early Access, and the system
  requirements; the footer carries the Project Gutenberg and Wiktionary trademark notice.
- Still true: no requests to other origins, no console errors, no horizontal scroll from 375 px up, no face overflow,
  and both pages work without script.
- The change is committed, pushed to `origin/main`, and the Pages deploy for it succeeds.

## Design

### Facts (verified 2026-10-26)

- Assets (all by the user, Steam store/library set for app 1405483): `library_hero_3840x1240.png` (opaque, cream
  `#fff7ec` with a tilted graph-paper grid and tiles in `#674dad`, `#576172`, `#757947`, `#ae4038`, `#2f6ac3`,
  `#1c837e`, `#2a3042`, `#926611`, `#4c687f`; a blank blue tile at x 1511–2330, y 378–1028 sits at the exact center),
  `library_logo_853x720.png` (alpha: mascot and pixel wordmark), `shortcut_icon.ico` (PNG entries 16–256, 32 KB),
  `shortcut_icon_512.png` (alpha), `app_icon_184.jpg` (on solid blue), `linux_icons.zip` (16–512 PNG), capsules
  (`main_capsule_1232x706.png`, header, small, vertical, library capsule and header), `mac_icon.icns`, and
  `storepage_1405483_all_translated.json` (store copy in 30 languages).
- The Steam store page `store.steampowered.com/app/1405483/` is not public: it redirects to the Steam home page.
- Store copy (English): Early Access, complete for daily reading on Windows and Linux; about half a year in Early
  Access; next: whole-word splitting for Chinese and Japanese, more dictionaries and learning languages, macOS,
  syncing between computers, touch and controllers (Steam Deck). 24 free offline dictionaries for 12 learning
  languages; interface in 12 languages; light and dark schemes. Requirements: Windows 10 64-bit (2004+) or 11; 64-bit
  Linux (Ubuntu 22.04+) or SteamOS 3; keyboard and mouse; internet only to download books and dictionaries.
- No image tool is installed except `sharp` in `node_modules` (Astro's dependency), which reads PNG/JPEG and writes
  WebP/PNG but not ICO; the ICO's entries are PNGs, so a trimmed ICO can be written by hand.
- VUI.Web 0.3.0: the pivot's `leave` turns a wall's tiles away and hides the page afterwards; anything in the page
  outside the wall does not move. `TopBar` renders and the pivot toggles `.vui-pivot-sub` on the nav whenever a sub
  page is on show.

### Design

- Banner: `src/components/Banner.astro` in the home `Page` before `HomeWall`. A clipping box whose height is
  `clamp(280px, 100vw × 1240/3840, 64svh)` holds a stage with the art's aspect ratio, at least as wide as the box and
  centered both ways, so the art covers the box like `object-fit: cover` while the logo, placed in stage percentages
  (center 50% / 53.5%, 62% of its height), stands on the blue tile with its wordmark ending at the tile row below. The page's `h1` is the logo image (alt "Errant Ant"). A mask fades
  the bottom into the page.
- Banner motion without touching VUI.Web: CSS only. It fades in with `@starting-style` on load and whenever the home
  page shows again, and fades out while `.vui-pivot-sub` is on the bar (`body:has(.vui-pivot-sub)`), so it leaves
  together with the turning tiles.
- Scheme (`src/styles/site.css`): canvas `#fff7ec`, a warm chrome, accent `#2f6ac3`; tile tokens take the art's
  colors (new `--tile-navy`); the reader tile keeps the app's green word hue explicitly.
- Assets into `public/`: `brand/hero.webp`, `brand/logo.webp`, `brand/mascot.webp` (WebP from sharp),
  `favicon.ico` (16/32/48 entries of `shortcut_icon.ico`), `apple-touch-icon.png` (180 px from `app_icon_184.jpg`),
  `social.png` (the main capsule as is). Head meta in `BaseLayout.astro`.
- Tiles (`HomeWall.astro`): the Coming soon tile shows the mascot and says Windows · Linux, its notice names Steam
  Early Access and macOS later; the language tile counts learning languages with the dictionaries on its back; a new
  Early Access group lists what comes next; About gains interface languages and requirements tiles; a bar item for
  Early Access.

## Scope

- In: `src/`, `public/`, README's description of the site, this task and the index.
- Out: VUI.Web, the privacy policy text, translating the site (the store translations are store copy), a Steam link
  (the store page is not public), the unused capsules, `mac_icon.icns` and the Linux icon set beyond the favicon.

## Execution slices

- [x] Assets converted into `public/`, old favicon removed.
- [x] Banner component with its motion; scheme and tile colors.
- [x] Head meta, tile copy, Early Access and About groups, footer notice, README.
- [x] Browser checks (desktop, phone, pivot to privacy and back, no script), build.
- [x] Commit, push, Pages deploy.

## Current state

Done: banner, palette, icons, social card and store facts are in, verified locally, committed and pushed to `main`;
the Pages deploy runs from that push.

## Validation

- Banner, colors, tiles: one desktop (1440×900) and one phone (375×812) run in the built-in browser against the dev
  server; measure the logo's box against the blue tile's box from the stage geometry and read one cropped screenshot
  per width for appearance. Very wide crop: one measurement at 2560×1000 that the stage covers the box.
- Pivot: click the Privacy policy tile and come back; read the banner's computed opacity during and after.
- Icons and meta: read the built `dist/index.html` head and fetch each referenced file from the dev server.
- Regressions: console errors, network requests to other origins, `scrollWidth` ≤ `innerWidth` at 375 px, face
  overflow via the existing engine (tiles' text inside faces), and the built HTML without script showing banner and
  tiles (no-JS check by reading `dist/index.html`, since the banner has no script).
- Deploy: `gh run watch` on the push's Pages run and a fetch of errantant.com afterwards.

Evidence (2026-10-26, dev server and `npm run build`):

- Banner: 1440×900 banner 465 px tall under the bar; logo box x 566–859, y 210–458 inside the blue tile's columns
  (559–866) and centered on it; 375×812 banner 280 px, stage 867 px wide covering it, logo 177×149 on the 185×147
  tile, `scrollWidth` 375; 2560×1000 banner 640 px (64svh) with the stage covering it. Cropped screenshots at both
  widths: the logo stands on the blue tile, the bottom fades into the page grid without a color step.
- Motion: the banner's CSS transitions read from `getAnimations()`: load and return 0 → 1, moving to the privacy page
  1 → 0 (the pane was hidden, so timing was checked by keyframes rather than by frames).
- Tiles: no face overflows at 1440 or 375 px (after fixing the mascot's stretched width and the interface tile's
  two-line title); the Coming soon tile shows the mascot.
- Requests: all same-origin (`/brand/*.webp`, screenshots, scripts); no console errors.
- Build: `dist/index.html` head has favicon.ico, apple-touch-icon, theme color and the Open Graph and Twitter tags
  with absolute `https://errantant.com/` URLs (`/privacy/` on the privacy page); no `favicon.svg` reference; the
  banner and its `h1` logo are plain HTML, so they show without script.

## Decisions

- 2026-10-26: The user delegated every choice of this task ("ultimate freedom") and asked for commit, push and
  publish right after the design.
- 2026-10-26 (delegated): The art becomes a full-bleed banner above the tile wall, with the logo on its center tile.
  Alternative: the art inside a `full` group tile, which would move with VUI's tiles but lose the full bleed.
- 2026-10-26 (delegated): The page and tiles take the art's palette and a blue accent. Alternative: keep the app's
  Paper scheme colors, which leaves a gray-cream seam at the banner's edge.
- 2026-10-26 (delegated): The site names Steam Early Access for Windows and Linux with macOS later, as the store copy
  says, without a link until the store page is public. Alternative: keep "macOS · Windows · Linux" without Steam.
- 2026-10-26 (delegated): The screenshot tile stays the first tile row, right under the banner (the user's earlier
  choice that it leads the wall).
