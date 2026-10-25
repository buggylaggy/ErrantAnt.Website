---
id: 20261025-coming-soon-homepage
status: completed
queue: current
depends_on: []
created: 2026-10-25
updated: 2026-10-25
---

# Goal

The errantant.com homepage: an Astro site deployed to GitHub Pages whose one page presents Errant Ant with a Coming
soon button, drawn in the same visual language as the current Errant Ant desktop app (paper scheme, graph-paper grid,
translucent bars, Metro-style tiles). The tile language lives in a new shared package, VUI.Web, that the Buggy Laggy
site adopts later.

## Acceptance

- The repository is an Astro static site like Buggy Laggy's: `npm install`, `npm run dev` and `npm run build` work, the
  build writes `dist/index.html` and `dist/CNAME` (`errantant.com`), and a GitHub Actions workflow deploys each push to
  `main` to GitHub Pages. The README says how to develop, build, update VUI.Web and turn on Pages for the custom domain.
- The top bar, tile groups, tiles and grid come from the `vui-web` package, built in `/home/yuki/project/VUI.Web` and
  consumed as an exact packed version from this repository's `packages/` folder; the site holds only its scheme,
  content and Errant Ant-specific parts.
- The page shows a translucent top bar with the "Errant Ant" wordmark and section links over a graph-paper grid, then
  tile groups that say what Errant Ant is and does, and a footer naming Buggy Laggy Limited with a link to
  buggylaggy.com.
- The look is recognizably the app's start page in its default Errant Ant Paper scheme: paper canvas, ink and muted
  text, leaf accent, the app's tile colors, serif type throughout (a system serif stack; no web font is served), small
  tile corners and gaps.
- A Coming soon button is the page's one call to action. It responds to hover and press like an app tile, and a click
  raises a bottom-bar notice saying Errant Ant is not released yet and is coming to macOS, Windows and Linux; the
  notice sinks again after about 3 s.
- On load the grid fades in and makes one random quick-to-slow move/turn/zoom, then stands still; the tiles enter with
  a short stagger. Nothing animates after that except in response to input.
- The section links scroll to their tile groups.
- The page works from a phone width of 375 px up to a wide desktop: no horizontal page scroll, groups wrap instead of
  overflowing, text stays inside its tiles.
- Every claim on the page is true of the current app (README of the ErrantAnt repository); nothing promises a date,
  a price or a feature the app lacks.
- The built page makes no requests to other origins, and the browser console shows no errors.

## Design

### Facts from the app (verified 2026-10-25 in `/home/yuki/project/ErrantAnt`)

- Default scheme `src/ErrantAnt.App/Resources/Tunables/Schemes/errantant-paper.json`: canvas `#FAF9F6`, chrome
  `#F0EFEB`, ink `#292B2D`, muted `#656966`, leaf (accent) `#28654D`, leaf pale `#B8D8C6`, line `#D9D7D0`, elevated
  surface `#FFFFFF`, accent foreground `#FFFFFF`, button hover `leaf` at 90%.
- Tile colors in `Resources/Tunables/Defaults/shell.json`: covers `#28654D`, `#B0573A`, `#3E4A5C`, `#A8812B`,
  `#6B3F5E`; tiles cobalt `#1F5FBF`, brick `#A8322A`, teal `#0B7A75`, violet `#5B3FA8`, steel `#4A5568`, slate
  `#3E5C76`, olive `#6B6F3A`, ochre `#8A5A00`. Tile text white; default tile opacity 0.85, corners 5, gap 6 (at the
  app's 960 by 600 base scale).
- VUI tiles (`VUI.Avalonia/.../Defaults/controls.json`, `motion.json`): cell 53 px, group gap 42 px, 4 columns per
  group, group header 12 px, edge `#FFFFFFB3` 1.5 px, press scale 0.96 with a 10° tilt toward the pressed point,
  enter stagger 40 ms; pivot (wordmark) 36 px Light.
- Start page grid (`Controls/GridBackdrop/GridBackdrop.cs`, `shell.grid.*`): ink `fg.default`, major spacing 140 with
  4 minor divisions, major width 1.4 at opacity 0.1, minor width 1 at half that; one shot per visit with turn 6–25°,
  shift 40–180, zoom 1.15–1.5, scale 0.7–1.6, duration 1500–3600 ms, quartic ease-out; the bars show it blurred.
- Start page layout (captures in `ErrantAnt/.tmp/gui/b18-hub.png`): wordmark and muted pivot items in a translucent
  bar, small muted group headers, tiles with a glyph top left and a label bottom left, book tiles with an uppercase
  label ("CONTINUE READING"), a serif title and a caption.
- The app describes itself (`UI.Settings.AboutSummary`): "A reader for language learners that counts each word you
  meet and keeps the sentences you met it in."
- The reader's word-coloring hue lives in `Controls/ReaderSurface/WordColoring.cs` (new words start a light azure and
  fade by five meetings; five levels); saved phrases are underlined.
- VUI.Avalonia's consumer pattern (ErrantAnt README, memory): a standalone repository with its own versions; each
  consumer pins an exact version restored from its own `packages/` folder; a consumed version is never overwritten.

### Facts from the reference and the repositories

- `/home/yuki/project/buggylaggy/website` (public `buggylaggy/website`, serves buggylaggy.com): Astro 7.3.1 with
  `output: "static"` and `site`, `src/layouts/BaseLayout.astro`, `src/pages/index.astro`, `src/styles/global.css`,
  `public/CNAME`, and `.github/workflows/deploy.yml` (checkout v7, `withastro/action@v6`, `actions/deploy-pages@v5`,
  Pages and id-token permissions, one `github-pages` concurrency group). npm's current Astro is 7.4.2; Node 24.20 and
  npm 12.1 are installed here.
- This repository (`jusvail/ErrantAnt.Website`) holds only `README.md` and is **private**; GitHub Pages from a private
  repository needs a paid plan, otherwise the repository has to be public. The README states this; the choice stays
  with the user at deployment.
- `/home/yuki/project/VUI.Web` does not exist yet.

### Shared package: VUI.Web

- New local repository `/home/yuki/project/VUI.Web` (`git init`, nothing committed), npm package `vui-web` 0.1.0,
  shipped as Astro source with `astro` as a peer dependency:
  - `src/vui.css`: the `--vui-*` tokens (canvas, chrome, ink, muted, accent, grid ink, tile opacity, edge; cell, gap,
    corner, group gap, page padding, type sizes) with neutral defaults a site overrides, the phone breakpoint that
    fits a 4-column group to the viewport, and the base styles of the bar, groups, tiles and grid.
  - `src/TopBar.astro`: a sticky translucent bar with a backdrop blur over the grid; its content is a slot.
  - `src/TileGroup.astro`: a section with an id, a small header and a 4-column dense tile grid.
  - `src/Tile.astro`: one tile from `cols`, `rows`, `color` and optional `label`/`title`/`caption`, a default slot for
    drawn content; with `href` it renders a link and with `button` a button, and those interactive tiles show the edge
    on hover and press to 0.96 with a tilt toward the pointer. Every tile takes part in the entrance stagger; without
    script the tiles still appear.
  - `src/GridBackdrop.astro`: the fixed graph-paper layer with the app's grid metrics and its one-shot script.
  - `README.md` (components, props, tokens, how a consumer packs and pins a version), `.gitignore`, packing with
    `npm pack --pack-destination artifacts`.
- Consumers keep `packages/vui-web-<version>.tgz` and depend on `"vui-web": "file:packages/vui-web-<version>.tgz"`, so
  Pages builds need no access to another repository; a change is a new version, never an overwritten one.
- Only what both sites use goes into the package; the Coming soon tile and its notice stay in this site until another
  consumer needs them.

### This site

- Files: `package.json` (`astro` ^7.4.2, `vui-web`), `package-lock.json`, `astro.config.mjs` (`output: "static"`,
  `site: "https://errantant.com"`), `.gitignore` as the reference, `packages/vui-web-0.1.0.tgz`, `public/CNAME`
  (`errantant.com`), `public/favicon.svg` (four small tiles, one in leaf), `.github/workflows/deploy.yml` (the
  reference's), `src/layouts/BaseLayout.astro` (head: title, description, theme-color `#FAF9F6`, favicon; imports
  `vui-web/vui.css` and the site styles), `src/styles/site.css` (the paper scheme as `--vui-*` values, the tile
  palette, the serif stack, reader sample, map drawing, notice and footer), `src/components/ComingSoonTile.astro`
  (the tile-button and the bottom-bar notice), `src/pages/index.astro` (bar, groups, tiles, footer).
- Type: no web font. One serif stack for all text, `ui-serif, "Iowan Old Style", "Palatino Linotype", Palatino,
  Georgia, serif`; the wordmark asks for weight 300 and falls back to regular where the system serif has no light
  weight.

### Page (delegated)

- Top bar: the "Errant Ant" wordmark at pivot size, then muted section links Reading, Words, Map that turn ink on
  hover.
- Tile wall: three groups of 4 columns by 4 rows, side by side and wrapping. Tiles at 0.85 opacity over the grid like
  the app, white text, uppercase labels, serif headlines.
  - Coming soon: a 4×2 slate hero with label "A READER FOR LANGUAGE LEARNERS" and the About summary as its headline;
    a 2×2 leaf Coming soon tile-button (glyph top left, "Coming soon" bottom left, caption "macOS · Windows ·
    Linux"); a 2×1 ochre "EPUB · PDF · TXT" tile (your own books, or free ones from Project Gutenberg); 1×1 tiles "12"
    UI languages and "0" accounts.
  - Reading: a 4×3 white reader sample, the opening sentence of Pride and Prejudice (public domain) with words in the
    app's word-coloring levels and one saved phrase underlined; a 2×1 brick "Paged reading" (a turned page counts as
    read); a 2×1 teal "Word panel" (notes, an offline English dictionary, every sentence you met the word in).
  - Map: a 4×2 violet Map tile with a small drawn board (word nodes, links, a pen stroke); a 2×2 olive knowability tile
    ("15 of 20", known); a 2×1 steel "Local-first" (no account, no network needed); a 2×1 terracotta "Import word
    lists" (typed, pasted, TXT/CSV/TSV).
- Only the Coming soon tile is interactive. Informational tiles are static after their entrance.
- Coming soon notice: a fixed bottom bar drawn like the top bar that rises in on click, reads "Errant Ant isn't out yet
  — it's coming to macOS, Windows and Linux.", and sinks after 3 s (a repeat click restarts the timer).
- Footer: muted small text, "© 2026 Buggy Laggy Limited" and "Made by Buggy Laggy" linking to https://buggylaggy.com.

## Scope

Included: the new VUI.Web repository as described, this site's files listed above and a rewritten `README.md` (what
the site is, local development, build, updating VUI.Web, GitHub Pages setup with the custom domain and the
private-repository note).

Excluded: creating a GitHub repository for VUI.Web, GitHub Pages settings, repository visibility, DNS, commits and
pushes (the user deploys), analytics, a mail signup or any form, web fonts, a dark scheme, translations, screenshots
of the real app, and any change in the ErrantAnt or VUI.Avalonia repositories. The Buggy Laggy changes are their own
tasks in that repository (`20261025-errant-ant-listing`, `20261025-metro-tile-style`).

## Execution slices

- [x] Tracer: VUI.Web with `vui.css` and `Tile.astro`, packed to a tarball; this site's skeleton (package files, Astro
  config, `.gitignore`, `public/CNAME`, workflow, layout) installs it from `packages/` and `npm run build` renders one
  package tile into `dist/index.html`. Proves that Astro compiles `.astro` source from a packed `file:` tarball
  dependency, the boundary the shared package rests on; the other components and the page come after.
- [x] VUI.Web breadth: `TopBar`, `TileGroup`, `GridBackdrop`, interactive tiles and the entrance; README; repacked as
  0.1.0 (nothing consumed yet, so the tracer's tarball may be replaced until this slice ends).
- [x] Page: scheme, favicon, bar, the three groups with their tiles, footer at desktop and phone widths.
- [x] Coming soon tile-button and its bottom-bar notice.
- [x] `README.md` (and VUI.Web's README).

## Current state

Completed 2026-10-25. VUI.Web 0.1.0 exists at `/home/yuki/project/VUI.Web` (`git init`, nothing committed) and is
pinned here as `packages/vui-web-0.1.0.tgz`; nothing is committed in either repository. Deployment (Pages settings,
visibility, DNS) is the user's step.

Implementation notes: the tile cell is `clamp(72px, 6.2vw, 120px)` so three groups and the page padding (834 units)
fit from about 1280 px; the grid is a canvas that draws only the lines crossing the window (an oversized CSS layer
would need a texture of several thousand pixels square); the entrance stagger is CSS-only (`nth-child` steps per group,
an offset per group) so tiles never flash before a script runs; on phones the scroll room under the bar is 80 units
because the bar can take two lines. Astro inlines a component's script where the component is first used, so a
`<script>` sits among the first group's tiles and shifts later stagger steps by one; the order stays the same.

## Validation

- Build and deployability: `npm run build` passes; `dist/index.html` and `dist/CNAME` exist; the workflow matches the
  reference's (a diff); `npm ci` from a clean `node_modules` restores `vui-web` from `packages/`. The Pages deployment
  itself is the user's step and stays unverified here.
- Page and look: `npm run preview` in the built-in browser; the network list shows only same-origin requests, the
  console has no errors; one cropped desktop capture (about 960×600) judged against the app capture `b18-hub.png`.
- Grid shot: the grid layer's computed transform differs between load and settle and stays fixed afterwards.
- Coming soon: a real click raises the notice (bar on screen, text present) and it is gone again after about 3 s.
- Section links: clicking Map scrolls its group into view below the bar.
- Phone: at 375×812 `scrollWidth` equals the viewport width and no tile's content overflows its box (a scripted
  `scrollHeight`/`scrollWidth` check per tile); one cropped capture.
- Claims: read the page text against the ErrantAnt README once.

Evidence (2026-10-25, Node 24.20, Astro 7.4.2, built-in browser pane):

- Tracer: a page with one `vui-web/Tile.astro` built from `file:packages/vui-web-0.1.0.tgz`; `dist/index.html` held
  the tile and the bundled CSS and press script.
- Build and deployability: `npm ci` from scratch then `npm run build` built 1 page; `dist/` has `index.html`,
  `CNAME` (`errantant.com`) and `favicon.svg`; the workflow is byte-identical to Buggy Laggy's; the lockfile pins
  `vui-web` 0.1.0 with the final tarball's integrity and `node_modules/vui-web/src` equals VUI.Web's `src`.
  `astro dev` answered 200 with the tiles. Pages deployment not exercised.
- Look: at 1440×900 the three groups sit in one row under the blurred bar over the grid, in the paper scheme, tiles
  in the app's colors; judged against `b18-hub.png`.
- Grid shot: canvas pixel hashes differ at 0.3 s and 1.2 s and are equal at 4.2 s and 5.2 s.
- Coming soon: a real click put the notice fully on screen (top 845 of 900) after 0.8 s and back off screen (900)
  after 3.8 s, with the text above; hover gives the 1.5-unit edge (`rgba(255,255,255,.7)` inset); a real pointerdown
  sets the tilt variables and the `:active` rule scales to 0.96 with the tilt (the held press itself was not
  captured, since the driver's click is instantaneous).
- Section links: at 375×812, Reading lands below the bar; Words scrolls to the end of the page (max scroll 559).
- Phone 375×812: `scrollTo(200, 0)` leaves `scrollX` at 0; no tile's content overflows after "Vocabulary ·
  Phrases" moved from the title to the label (it wrapped to two lines); one cell is 81.25 px.
- Requests and console: only `localhost:4321` requests (document and CSS; scripts are inline); the console showed
  no errors, only a `getImageData` warning caused by the validation probe.
- Claims: page text checked against the ErrantAnt README (formats and Gutenberg, a page counts as read when turned,
  Word panel contents, twelve UI languages, no account and local data, Map contents, Import formats).

## Decisions

- 2026-10-25: The user asked for an Errant Ant homepage with a Coming soon button in the current app's UI style and
  delegated the design details ("you can free to design the detail").
- 2026-10-25: The user ruled out a custom font ("no need") and asked for a real project architecture because the site
  deploys to GitHub Pages, with `/home/yuki/project/buggylaggy/website` as the reference; the self-hosted subset fonts
  of the first design are dropped.
- 2026-10-25: The user chose a shared package for the tile components (over a copy per site), started now in
  `/home/yuki/project/VUI.Web` and pinned as a packed tarball in each site's `packages/`, so Errant Ant is built on it
  from the start and Buggy Laggy adopts it later.
- 2026-10-25: The user asked to build this page first, before the Buggy Laggy tasks.
- 2026-10-25 (delegated): Astro static site with the reference's layout and Pages workflow, so both public sites build
  and deploy the same way; alternative: plain files with a Pages artifact workflow.
- 2026-10-25 (delegated): The page is the app's start page (bar wordmark and links, grid shot, tile groups) rather
  than a conventional centered hero, because that layout is the app's identity; alternative: a centered hero with a
  screenshot.
- 2026-10-25 (delegated): Only the default Errant Ant Paper scheme, no dark variant; alternative: a dark scheme from
  `prefers-color-scheme`.
- 2026-10-25 (delegated): Coming soon is a leaf tile-button that raises a bottom-bar notice, because a dead or
  disabled button reads as broken; alternative: a disabled button.
- 2026-10-25 (delegated): The footer names Buggy Laggy Limited and links to buggylaggy.com, because the user lists
  Errant Ant on the Buggy Laggy home page; alternative: no maker credit.
- 2026-10-25 (delegated): English only, no signup form.
