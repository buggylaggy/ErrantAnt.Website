---
id: 20261027-policy-pace
status: completed
queue: current
depends_on: [20261026-live-banner]
created: 2026-10-27
updated: 2026-10-27
---

# Goal

The privacy policy loses its opening sentence, and moving to and from the privacy page is quicker than it became once
the banner's tiles joined the home wall.

## Acceptance

- The policy goes from its effective date straight to "Personal data we collect"; the sentence "This Privacy Policy
  explains what information the Errant Ant app handles and how." is gone.
- The home wall's tiles turn away (opening the policy) and turn back in (leaving it) in clearly less time than now;
  a wall of few tiles keeps VUI's timing.
- Build is clean; no console errors.

## Design

### Facts (verified 2026-10-27)

- vui-web's panel staggers every tile of a wall by its index: 40 ms apart turning in, 25 ms turning away
  (`src/engine/panel.ts`, VUI.Avalonia's `motion.tile.enterStagger` / `exitStagger`). The home wall has 45 tiles since
  the banner (it had 14), so the last tile starts turning in after 1.76 s and away after 1.1 s.
- The privacy page itself rises and sinks on one spring (`presets.settle` / `presets.exit`), about 0.4 s.
- The pivot and the panel are VUI.Web's (the delegated decision of 20261025-big-tile-and-privacy); the site pins its
  packed tarball.

### Changes

- `PrivacyPolicy.astro`: drop the opening sentence.
- VUI.Web 0.3.1: a wall of more than 24 tiles shares 24 tiles' stagger time among all of them (the gap between two
  tiles shrinks; the wave stays even), for the tiles and the group titles. The home wall's turn-in spread goes from
  1.76 s to 0.94 s, its turn-away from 1.1 s to 0.59 s. Pack, pin `packages/vui-web-0.3.1.tgz`, remove 0.3.0.

## Decisions

- 2026-10-27: The user asked to drop the policy's opening sentence and to speed the privacy page's enter and exit up
  "a little bit".
- 2026-10-27: The user allowed editing VUI.Web for the cap, bumping it to 0.3.1, packing it and pinning it here.
- 2026-10-27: The speed-up caps a wall's total stagger at 24 tiles' worth, so small walls keep VUI's 40/25 ms; the
  alternative was smaller stagger constants for every wall, which would part from VUI.Avalonia's tunables.

## Validation

- `npm run build` is clean and `dist/privacy/index.html` lacks the sentence.
- Browser: open the policy from the home page and go back, reading the last tile's turn timing (its transform settles)
  against the 45-tile wall's expected spread; no console errors.

## Current state

Completed 2026-10-27; committed and pushed at the user's request. The policy goes from its effective date straight to
"Personal data we collect" (`dist/privacy/index.html`; build clean). VUI.Web 0.3.1 (`/home/yuki/project/VUI.Web`,
commit `9cc36fd`: `panel.ts`, `package.json`, README pin example) is packed and pinned as `packages/vui-web-0.3.1.tgz`
(0.3.0 removed; `node_modules/vui-web/src` equals VUI.Web's `src`). In the browser at 1440×900 the 45-tile home wall
scheduled its tiles 13.33 ms apart when the policy opened (25 × 24 / 45; last at 587 ms, was 1100) and 21.33 ms apart
on Escape back (40 × 24 / 45; last at 939 ms, was 1760); walls of 24 tiles or fewer keep the factor 1. The pane was
hidden, so the springs themselves did not run; their presets are unchanged. No console errors.
