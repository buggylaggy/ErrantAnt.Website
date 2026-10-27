---
id: 20261026-plain-copy
status: completed
queue: current
depends_on: [20261026-brand-assets-refresh]
created: 2026-10-26
updated: 2026-10-26
---

# Goal

Strip the explanatory, "agentic" copy out of errantant.com: short tile text only, no Early Access group, a plain
footer, and a professional privacy policy for the Errant Ant app alone. Then commit and push.

## Acceptance

- No Early Access group or bar item; the About group holds only the Privacy policy tile, with no caption.
- Tiles carry a title, a short caption or a figure; no sentence-long captions and no back faces that only explain.
  Back faces that show app-like data (figures, terms, the last lookup) stay.
- The footer is the copyright line alone; the trademark notice and the second "Made by" link are gone.
- `/privacy/` is a standard privacy policy for the Errant Ant app: title, effective date, short sections (data we
  collect, data on your device, third-party services, deleting your data, changes, contact). It does not name the
  maker, does not cover the website and does not mention GitHub Pages.
- Build is clean, both pages render with no face overflow, the change is committed and pushed, and Pages deploys.

## Design

### Facts (verified 2026-10-26 in the ErrantAnt repository)

- The app's Settings → About → Privacy policy opens this page (`settings.privacyPolicyAddress`).
- Network use: Project Gutenberg (`https://www.gutenberg.org`) for the catalog and books; dictionaries and
  translation packs from GitHub releases (`buggylaggy/errantant-dictionaries`). No other endpoints, no Steam SDK.
- Translation runs on the device (Bergamot models in the data folder).
- Settings → About → Reset all data clears the library but keeps downloaded dictionaries and translation packs.
- Gutenberg's privacy policy: `https://www.gutenberg.org/policy/privacy_policy.html` (200).

### Changes

- `HomeWall.astro`: drop the Early Access group, the two extra About tiles, the privacy caption, and every
  explanatory back face; shorten captions to noun phrases (`·`-separated where they list).
- `ComingSoonTile.astro`: notice says only that it isn't out yet and is coming to Steam.
- `SitePage.astro`: drop the Early Access bar item and the trademark notice; footer is `© 2026 Buggy Laggy Limited`;
  privacy page description is plain.
- `PrivacyPolicy.astro`: rewritten app-only, structured like GitHub's privacy statement (title, effective date,
  plain sections).
- `site.css`: drop the footer link and `.site-legal` rules; style the policy's `h1`.

## Decisions

- 2026-10-26: The user asked to remove the Early Access group, the System requirements and Interface languages tiles
  and the privacy tile's caption, to cut descriptive copy site-wide, and to rewrite the privacy policy app-only,
  without the maker and without the website; then commit and push. The remaining wording is delegated.
- 2026-10-26 (delegated): The footer keeps only the copyright line. The alternative was keeping the buggylaggy.com
  link, but it repeated the company name.

## Validation

- `npm run build` is clean.
- In the browser: no `.vui-tile-face` overflows, the bar has no Early Access item, and the privacy page text has no
  "GitHub Pages", "website" or "Buggy Laggy".
- `git push` succeeds and the Pages run for the commit succeeds.

## Current state

Completed 2026-10-26. Evidence: `npm run build` clean; in the browser at 1440×900 the home page has the groups
look-inside, coming-soon, reading, words and about, the bar has no Early Access item, no tile face overflows, the
footer is the copyright line alone, and the privacy page's headings are the six planned sections with none of
"GitHub Pages", "website", "Buggy Laggy" or "errantant.com" in its text.
