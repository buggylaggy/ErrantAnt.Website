---
id: 20261025-big-tile-and-privacy
status: completed
queue: current
depends_on: []
created: 2026-10-25
updated: 2026-10-25
---

# Goal

Second round of homepage refinements from the user's review of the live page: the screenshot tile becomes one big
tile alone in its row, nearly full width and centered, under a deep translucent curtain for contrast; and a Privacy
policy tile and bar link open a standalone sub page with the app's VPivot page animation.

## Acceptance

- The screenshot tile is alone in the first row, centered at about 1/1.16 of the row's width and as tall as the
  screenshot's shape up to the first screen; a black curtain at 70% covers it at rest and lifts on hover, when it
  grows ×1.16 from its center to the row's full width and pushes the groups below down, while the page fills with
  the blurred screenshot as before.
- A Privacy policy tile and a "Privacy policy" item after "Words" in the top bar open `/privacy/`, a page of its own
  (its own HTML file, reachable directly, working without script).
- Going there and back animates like the app's VPivot: the header labels spring to their new places (the chosen
  item large and first, the others small, hidden on the sub page), the section color wipes into the bar behind a
  slanted edge and tints the page, the start page's tiles turn away (the clicked tile dives) while the sub page
  rises, a back button appears, and Back (button, Escape, or the browser's Back) reverses it all with the tiles
  returning and the grid making a new shot.
- The policy text, drafted from verified facts, covers the website and the app and is marked for the user's review.
- Still true: no requests to other origins, no console errors, no horizontal scroll from 375 px up, no face overflow,
  and without script both pages show their own content and the links navigate.

## Design

### Facts (verified 2026-10-25)

- Live page (`17d5ce8`, VUI.Web 0.2.0 `fbef043`): the 6×4 "A look inside" group shares the first row with Coming soon
  and Reading at 1440 px; the bar links are in-page anchors (Coming soon, Reading, Words).
- Astro 7.4.2 ships `ClientRouter` (`astro/components/ClientRouter.astro`) with the navigation lifecycle events
  (`astro:before-preparation`, `astro:after-swap`, `astro:page-load`), so a link can stay an ordinary link to a real
  page while script runs an exit animation before the swap and an entrance after it; without script it is a plain
  page load.
- Facts a privacy policy rests on:
  - Website: static files on GitHub Pages under `errantant.com` (DNS at Cloudflare, records DNS only, not proxied); no
    cookies, analytics, forms or third-party requests (all assets same-origin). GitHub, as host, receives visitors'
    requests and IP addresses under its own privacy statement.
  - App (ErrantAnt README and source): no account; reading data, notes and boards stay in the local application-data
    folder; network use only when the user downloads books or the catalog from Project Gutenberg
    (`GutenbergSource`, `https://www.gutenberg.org`) or dictionaries from
    `https://github.com/buggylaggy/errantant-dictionaries/releases` (`Resources/dictionaries.json`); no telemetry
    or analytics code found.
  - Buggy Laggy's public contact address on buggylaggy.com: `dev@buggylaggy.com`.
- App page navigation (read from code 2026-10-25; V = `VUI.Avalonia/src/VUI.Avalonia`, A = `ErrantAnt/src/ErrantAnt.App`):
  - Header (V `Controls/VPivot/VPivot.cs:156-185`, `PivotSlot.cs:39-61`, `controls.json:95-103`): every label is
    36 px Light; the selected item takes the large first place, the others follow in their natural order (no
    rotation) drawn at scale 0.42 and opacity 0.55 on the large label's baseline, gap 27, left inset 30. On a
    section page like Library the others stay at their small places at opacity 0 and take no clicks. A change
    springs each label's center, scale and opacity to its new place on settle, no stagger; Back reverses. A small
    label under the pointer grows ×1.12 to opacity 1 on the hover spring.
  - Bar wipe (V `VPivotBackdrop.cs:97-117`): direction +1 when the new item comes later in the item list (from the
    start page to a section), else −1. For +1 the edge's top point is at x = (W + 60)(1 − p), its bottom point 60 px
    further left; the new color lies right of the edge, the old one left, mirrored for −1; p springs 0 → 1 on
    settle. Bar tint 0.2, page tint 0.112 cross-faded linearly by p. Back to the start page wipes the color away
    from the left. The clicked tile's wash fades as the wall leaves.
  - Content (V `VTilePanel.cs:356-416`, `TileMotion.cs:160-224`, `VContainer.cs:116-168`, `Transit.cs:21-37`):
    everything starts in the same frame. Leaving tiles run away → +1 on exit (+28 px X, rotateY +70°) after 25 ms ×
    index; the clicked tile dives instead (scale ×5, opacity → 0, exit) while its hover growth relaxes; group headers
    fade on exit. The new page rises from opacity 0 and Y +24 → 0 on settle. Back: the page sinks to Y +24 and fades
    on exit; the start page's tiles return from +1 (and the dived tile from ×5) on settle, 40 ms × index.
  - Back button (A `Shell/StatusBar/StatusBarView.axaml:51-60`, `MainWindow.cs:179-196`): a 25 px button with a
    thin left arrow, shown instantly on every page but the start page; hover: accent at 90% and ×1.16 over 120 ms;
    Escape goes back.
  - Grid (A `Controls/GridBackdrop/GridBackdrop.cs`): shown on the start page only; leaving fades it out on exit,
    each return fades it in with a new shot.
  - Curtain (V `Controls/VTile/VTile.cs:411-449`, `VTile.axaml:48-57`): `#000000B3` (black at 70%) over the whole tile
    when text would meet a picture, fading over 120 ms linearly; hover neither adds nor lifts it.

### Design

- One site shell, two page files (delegated): `src/layouts/SitePage.astro` renders the bar, backdrop, both pages and
  the footer; `src/pages/index.astro` renders it with the start page current and `src/pages/privacy.astro` (built
  to `/privacy/index.html`) with the privacy page current; the other page is `hidden` in each file. Script moves
  between them in place (history `pushState` and `popstate`), so both pages can animate at once as in the app;
  without script the links are ordinary page loads. Alternative: Astro's `ClientRouter`, which swaps the document
  and makes the app's overlapping exit and entrance hard.
- VUI.Web 0.3.0:
  - `TopBar` becomes VUI's pivot, from data: `items` (label, href, and for pages a page id and an accent; the first
    item is the start page) and `current`. All labels share the large size; the current one is first and large, the
    others small (0.42) at opacity 0.55 in their natural order, and on a sub page the others are hidden (opacity 0,
    no clicks); a small label under the pointer grows ×1.12 to full opacity. A back button (thin left arrow, 25 units,
    accent hover ×1.16 over 120 ms) shows instantly before the title on any page but the start page.
  - `Page`: a page's content (`id`, document `title`, whether the grid shows); `Pages` stacks them in one grid cell
    so a leaving and an arriving page overlap.
  - `engine/pivot.ts`: the navigation. Header labels move by measured first/last positions on the settle spring;
    the wipe follows VPivotBackdrop (direction by item order, 60-unit slant across the bar, p on settle, bar tint
    0.2, page tint 0.112 cross-faded by p; the start page's accent is none); a page with a tile wall sends its tiles
    away (+1 on exit, 25 ms × index, the clicked tile diving ×5) and brings them back (settle, 40 ms × index); any
    other page sinks (Y +24, fade, exit) or rises (from Y +24, settle). The leaving page is pinned where it was while
    the window scrolls to the arriving page's last position (top on a first visit). Escape and the back button go
    back. The grid shows only on pages that ask for it and while no picture fills the page.
  - `TileWall` gains leave and return; its live and roll timers rest while its page is away.
  - `TileGroup full`: a group as wide as the wall whose single tile is centered at 1/1.16 of that width, takes its
    height from `--vui-aspect` up to the first screen, and grows from its center; `PictureRoll curtain` adds VUI's
    curtain (`#000000B3`, 120 ms linear) that lifts while the tile is emphasized.
- This site (delegated details):
  - The screenshot group keeps its title "A look inside"; captions stay in their dark chip, at the tile title size on
    the big tile; pictures keep their top in view when the first screen limits the height.
  - A new group "About" after Words holds a 2×1 steel (`#4A5568`) "Privacy policy" tile ("No cookies, no tracking;
    your data stays with you"); steel is also the page's accent, so the hovered tile's wash hands over to the page
    tint.
  - `/privacy/`: document title "Privacy policy — Errant Ant"; a reading column (max about 680 px) in the site's
    serif with the sections: last updated 25 October 2026; who (Buggy Laggy Limited) and what it covers (errantant.com
    and the Errant Ant app, not yet released, described as it stands); this website (no cookies, analytics, forms or
    third-party requests; GitHub Pages hosts it and receives requests and IP addresses under GitHub's privacy
    statement, linked); the app (no account; books, reading history, words, notes and boards stay in a folder on your
    computer; no analytics or crash reports; it goes online only when you ask, for Project Gutenberg books and the
    catalog and for dictionaries from GitHub, whose own policies apply); your data (nothing collected by us; delete
    yours with Settings → About → Reset all data or by removing the data folder); changes (this page and its date);
    contact dev@buggylaggy.com. Marked in the task as awaiting the user's review.
  - The footer sits outside the pages, on both.

## Scope

Included: VUI.Web 0.3.0 (packed and pinned, 0.2.0 removed from the site) and this site, including `/privacy/`.

Excluded: the Buggy Laggy restyle (its own task), commits and pushes unless asked.

## Execution slices

- [x] Tracer: VUI.Web pivot navigation between the start page and a bare `/privacy/` page: `TopBar` items and
  labels moving, `Page`/`Pages`, the wall's leave and return, the pinned leaving page and scroll, `pushState` and
  `popstate`. Proves two pages animating at once in one document with real history and the engine's timers resting
  while away; the wipe, back button, dive and content follow.
- [x] Wipe and page tint, back button and Escape, the clicked tile's dive, the grid per page, document titles.
- [x] Big tile: `TileGroup full`, centered growth in the projection, `PictureRoll curtain`, height and captions.
- [x] Privacy tile, About group and the policy text.
- [x] READMEs (VUI.Web, site); pack 0.3.0, pin it, remove 0.2.0.

## Current state

Completed 2026-10-26. VUI.Web 0.3.0 (`/home/yuki/project/VUI.Web`, uncommitted) adds `Page`, `Pages` and
`engine/pivot.ts`, turns `TopBar` into the pivot, gives `TileGroup` a `full` mode, `PictureRoll` a `curtain`, and the
panel leave, return, dive and centered growth; it is packed and pinned here as `packages/vui-web-0.3.0.tgz` (0.2.0
removed). The site renders both pages from `src/layouts/SitePage.astro` (`src/components/HomeWall.astro`,
`PrivacyPolicy.astro`; `src/pages/index.astro`, `privacy.astro`). The user reviewed the policy text on 2026-10-26 and
asked to commit and push.

Changes made during execution (recorded under Decisions): the site's cell override was removed; two tile texts were
corrected because the app no longer ships a dictionary.

## Validation

Headless Chrome over the DevTools protocol with trusted input (the built-in pane gives no frames while the session
window is closed), against `npm run preview`:
- Build: `npm ci` and `npm run build` pass with `vui-web-0.3.0.tgz` pinned (lockfile names it only); `dist/index.html`
  and `dist/privacy/index.html` exist, each with its own page shown and the other hidden.
- Big tile at 1440×900: alone in its row, equal margins left and right (±1 px), width ≈ row / 1.16, bottom within the
  first screen; curtain about 0.7 at rest and 0 while hovered; hovered, its width reaches the row's from the same
  center and the groups below move down by 0.16 of its height; leaving returns all to rest.
- Forward by the Privacy tile: the URL becomes `/privacy/`, the clicked tile's scale heads to ×5 and fades, other
  tiles turn away, the privacy page reaches rest (opacity 1, no offset); the header shows "Privacy policy" first and
  large with the other labels at opacity 0; the back button shows; mid-wipe the bar's tint edge lies inside the bar,
  and at rest the bar tint is steel at 0.2 and the page tint 0.112; the grid has no ink.
- Back by Escape, by the back button, and by `history.back()`: the URL returns to `/`, tiles are back at rest, the
  grid has ink again with a new shot, the header and tints are restored; forward again by the bar item works.
- Direct load of `/privacy/` shows the page; its back button brings the start page in.
- No-script: each file shows its own page; the bar link and tile navigate as plain links.
- 375×812 and 1440×900, both pages: `scrollTo(300, 0)` leaves `scrollX` 0; no tile face overflows; one capture of each
  page at each size, and one mid-transition capture.
- Requests and console: same-origin only; no errors.
- Policy: its statements checked once against the facts above.

Evidence (2026-10-26; headless Chrome 156 over the DevTools protocol with trusted input, against `npm run preview`;
VUI.Web 0.3.0 pinned):

- Build: `npm ci` then `npm run build` built `/index.html` and `/privacy/index.html`; the lockfile names only 0.3.0;
  `node_modules/vui-web/src` equals VUI.Web's `src`.
- Big tile at 1440×900: row 1339 px, tile 1154 px (1/1.16) with margins 92 and 92, bottom at 858 of 900; curtain on
  (opacity 1 over its 70% black) at rest and 0 hovered; hovered it is 1339 px wide around the same center (×1.160),
  the next group moves down 114 px (0.16 × 715 = 114), and `scrollTo(300, …)` leaves `scrollX` 0. At 375×812 it is
  296 px of 343 and grows to 343 around its center; its 31 px growth is less than the gap to the next group, so it
  pushes nothing (VUI's rule).
- Forward by the Privacy tile: 160 ms in, the start page is pinned (`position: fixed`), the privacy page is rising
  (Y 3.7 px, opacity 0.85) and the steel edge stands at x 232 of 1440 moving left; at rest the URL is `/privacy/`, the
  title "Privacy policy — Errant Ant", the labels read "Privacy policy" at 40 px with the others collapsed, the back
  button shows, the bar tint is `rgb(74, 85, 104)` over the whole bar, the page tint 0.112, the grid has no ink, the
  start page is hidden. The capture shows tiles turning away in 3D under the rising text.
- Back by Escape, by the back button (through history) and by `history.back()`, and forward again by the bar item and
  `history.forward()`: each lands on the right URL, title, labels (Errant Ant 40 px first, others 17 px at 0.55), tint
  (cleared, wiped from the left) and grid (ink again, a new shot); the start page's scroll comes back (680).
- Direct load of `/privacy/` shows the page with the grid hidden; its back button brings the start page in with its
  tiles at rest and the URL `/`.
- The rest still holds: the hero grows ×1.161 and pushes Coming soon's tiles down 30.3–30.7 px (expected 30.2) and the
  Reading and Words tiles right 61.5–62.4 px (61.9); the roll advances after 5 s and holds while hovered; the picture
  backdrop reaches 0.51 with the grid and bar paint out, and both return after; live tiles flip one at a time; the
  press tilts and the notice rises; Tab from the bar emphasizes the Coming soon tile (×1.16).
- No-script: `/` shows the start page with every tile's front face; clicking the Privacy tile loads `/privacy/`, which
  shows the policy, only the "Privacy policy" label and the back button.
- Both pages at 1440×900 and 375×812: `scrollTo(300, 0)` leaves `scrollX` 0, no tile face overflows.
- Requests and console: only `http://localhost:4321`; no exceptions or console errors in any run.
- Policy: each statement checked against the facts above and the current ErrantAnt README and source (no account;
  local data folder; Gutenberg catalog and books, dictionaries from GitHub releases on first use; Reset all data; no
  analytics code).

## Decisions

- 2026-10-25: The user asked for these changes after reviewing the live homepage (screenshot attached in chat).
- 2026-10-25: The big tile sits alone in the first row, centered at about 86% of the row's width (so ×1.16 reaches the
  full row) and as tall as the whole screenshot up to the first screen; on hover it grows ×1.16 from its center and
  pushes the groups below down. A deep curtain (black at 70%) covers it at rest and lifts on hover.
- 2026-10-25: The policy is drafted from the verified facts, covers the website and the app, names dev@buggylaggy.com as
  the contact and is dated 25 October 2026; the user reviews the text before it goes live (not legal advice).
- 2026-10-25: The user delegated the remaining details. Delegated choices, each reversible:
  - (delegated) One shell rendered by two page files, moving between them in place with history; alternative:
    Astro's `ClientRouter`.
  - (delegated) The pivot, pages, navigation, full-width group and curtain live in VUI.Web 0.3.0, since they are VUI
    behavior the Buggy Laggy site can reuse; alternative: site-only code.
  - (delegated) A new "About" group after Words with one 2×1 steel Privacy policy tile; alternative: a tile inside
    an existing group.
  - (delegated) `/privacy/` as the address; steel `#4A5568` as its accent.
  - (delegated) The back button sits before the large title (the web has no window-button row); alternative: a first
    row above the labels as in the app.
  - (delegated) The big tile's captions stay in dark chips at the tile title size, and its pictures keep their top in
    view when cropped by the first screen; alternative: a label and title over the curtain.
- 2026-10-26 (delegated, during execution): The site's cell override (`clamp(72px, 5.45vw, 120px)`) is removed: it
  only made room for the six-column group beside two others, and with the big tile alone in its row the package
  default puts three groups across from about 1280 px with larger tiles. Alternative: keep the smaller cell.
- 2026-10-26 (delegated, during execution): The app no longer ships a dictionary (ErrantAnt `0397036` and README:
  dictionaries per learning language, downloaded the first time they are turned on), so the Local-first back face now
  reads "Offline dictionaries — For every learning language, downloaded once when you turn one on" and the Word panel
  caption "How well you know a word, your own meaning, the dictionaries you turned on and every sentence you met it
  in". Alternative: leave the claims until a separate task.
- 2026-10-26: The user reviewed the privacy policy and asked to commit VUI.Web 0.3.0 and to commit and push the site.
