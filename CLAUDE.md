# Passage Plan Singapore · MT TITANIA

Offline PWA for building in-port passage plans in Singapore waters (anchorage/berth ↔ fairway). It computes waypoint courses, distances and ETAs, shows a vector chart of the port, and fills the ship's passage planning forms (Excel and PDF). UI is English; `README.md` is Indonesian.

Live: https://sailor-90.github.io/singapore-passage-plan/ (GitHub Pages, public repo).

## Layout

Static site, no build step, no package.json.

- `index.html` — the whole app, **6.2 MB**. CSS, markup (4 pages), two bundled libraries, embedded data and the app script.
- `sw.js` — service worker. Network-first, cache fallback (works offline at sea or at anchor).
- `manifest.webmanifest`, `*.png` — PWA manifest and icons.
- `README.md` — user-facing feature list and install steps.

## Working with `index.html` (read this first)

Never `Read` the whole file, and never `cat`/`grep` without `cut -c1-200`. Some lines are huge:

| What | Size |
|---|---|
| Chart SVG path lines (~lines 170–173) | 35K–636K chars each |
| Minified JSZip and pdf-lib (~lines 229, 244) | 97K / 524K chars |
| `const DATA=` port data (~line 248) | 520K chars |
| `const FORM_B64=` Excel template, base64 (~line 578) | 3.5 MB |
| `var TPL=` PDF templates, base64 (~line 905) | 630K chars |

Find them again with `awk 'length($0)>20000{print NR": "length($0)}' index.html`. Read ranges with `sed -n 'A,Bp' index.html | cut -c1-200`.

Edit with a small Python script that asserts the target string occurs exactly once, rather than the Edit tool on lines near the huge ones.

Structure: CSS (`<style>`, top of file) → `<section class="page">` × 4 (`pgData`, `pgRoute`, `pgPlan`, `pgChart`) → libraries → app `<script>`.

## Data and persistence

- `DATA` keys: `cal` (map calibration), `anc` anchorages, `fw` fairways, `pl` pilot/limit points, `tss`, `pbg` boarding grounds, `dg` dangerous-goods anchorages, `term` terminals, `aton` buoys, `net` (waypoint network: `nodes`, `edges`, `edgesAuto`, `lib` route library, `corr`), `berthDefault`.
- `localStorage` keys: `sgShip`, `sgOfficers`, `sgRoutes`, `sgNetAdd`, `sgWpNotes`, `sgTab`, `sgBerthPos`, `sgPdfSel`. Use `lsGet`/`lsSet`.
- Form inputs use `pp*` ids (`ppShip`, `ppDate`, `ppEtd`, `ppFrom`, `ppTo`, `ppSpd`, …).

## Core logic

- `rhumb(a,b)` — Mercator rhumb line, R = 3440.065 NM. `ppCalc()` builds the plan rows (course, distance, leg speed, ETA).
- Planned speed defaults to **8 kn** (`planSpd()`); per-leg speeds apply only when "speed per leg" is on (`useLeg()`).
- `P()`/`LL()` convert lat/lon ↔ SVG map coordinates using `DATA.cal`.
- Displacement comes from the `HYDRO` table by mean draft (`dispAt`). Air draught = `SHIP_HEIGHT` (32.3 m) − aft draft. `SHIP_HEIGHT` is declared twice in the script; keep both in sync.
- Output: Excel via JSZip into the `FORM_B64` template (ship's Form N 03); PDFs via pdf-lib onto `TPL` (passage plan, checklists N-03A/N-01/N-02, pilot card N-04); RTZ and CSV route export.
- Chart data is MPA PMC 02/2018 + PMC 03/2025, SPI Oct 2026. The tool is a planning aid and must be verified on corrected ENC/ECDIS, Notices to Mariners and Port Marine Notices (see README).

## Conventions and gotchas

- **Bump `VERSION` in `sw.js` whenever any file changes** (currently `passage-plan-sg-v8`), otherwise installed phones keep the old copy. After a push, phones need two opens to pick up the new version.
- Navigation numbers (routes, waypoints, UKC, buoys, drafts, hydrostatics) are safety-critical. Do not change or "fix" them unless explicitly asked.
- Fonts load from Google Fonts (IBM Plex); offline the system fallback is used.
- Everything is one file by design; do not introduce a build step or split files without being asked.

## Git workflow

- Public repo `sailor-90/singapore-passage-plan`. Claude commits locally; the user pushes with GitHub Desktop (Claude has no GitHub credentials).
- Commits must be authored as `sailor-90 <333464309+sailor-90@users.noreply.github.com>`. GitHub rejects the user's real Gmail (private email). After every commit, check `git log --format='%an <%ae>' -1` before telling the user to push.
- `graphify-out/` (graphify output) must never be committed or pushed to the public repo. It is excluded locally via `.git/info/exclude`.
