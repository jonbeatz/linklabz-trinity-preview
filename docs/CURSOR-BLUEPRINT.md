# CURSOR-BLUEPRINT.md — LinkLabz

**Living document.** This is the starting-point brief for the future real
LinkLabz rebuild (Cursor on Vader). Every revision to this repo adds a dated
entry to the Changelog at the bottom. Newest entry wins when notes disagree.

- System name: **LinkLabz** (Jon's personal link review system)
- Future product name (reserved, not in use): **Zift**
- Trinity mirror repo: `jonbeatz/linklabz-trinity-preview` (this repo)
- Forge mirror repo: `jonbeatz/linklabz-forge-preview` (Ravyn's challenger)
- Live Pages (this repo, serves `main` only): https://jonbeatz.github.io/linklabz-trinity-preview/
- Muse master (frozen until Jon approves an update): https://muse.ai/s/link-review-board-ec6lxgxhuxx4xf6

## 1. What LinkLabz is

A personal link-review board. Every link Jon saves gets: an honest verdict,
concrete harvestable patterns (stealable ideas for his own projects), and a
clear posture — **try it, park it, or skip it**.

Standing rules that must survive the rebuild:

- **Skipped** is a lane/posture; **Pass** is an action. Do not merge them.
- Every review carries an honest verdict + harvestable patterns, not just a verdict.
- Never gate reviews on licensing — nothing is being sold.
- Preserve all review data across imports and rebuilds.
- No separate harvested-skills section; harvested ideas route to projects/Goes-to.
- "Bookmark this" = save to the board's bookmark shelf (and Jon's `~/workspace/bookmarks.md` on Trinity's side).
- "Put it on my to-do list" = the board's integrated to-do (visual, check off / delete / reorder, severity levels, parking for way-down-the-line ideas). This is Jon's personal action tracker for board items — distinct from the shared Trinity+Ravyn to-do Doc and from the planned /todo page on digitalstudioz.com.

## 2. App map

| Area | What it does | Current state |
|---|---|---|
| Review board (master) | The 19 real reviews, verdicts, bookmarks shelf, to-do | Muse artifact `link-review-board`; Pages root `index.html` (tonal-grey retheme, Gradients 12) |
| Card Concepts lab | Experimental card layouts + interaction studies on the same 19 reviews | `/card-concepts/` — Card Swap, Cover Flow, plus the GodUI lab (Filter Lab, Review Digest, Priority Queue, Finish Lab, inertia dragging, card menus, mega-menu nav, floating quick-nav) |
| Bookmarks shelf | Saved links | Part of master board |
| To-do | Personal action tracker | Part of master board |

## 3. Data model + persistence keys

Core entities (lift exact field names and storage keys from the live artifact
source before building — the list below is the shape, not the schema):

- **Review**: id, url, title, source, date saved, verdict (`try` | `park` | `skip`), verdict text, harvest notes (patterns worth stealing), tags/lanes.
- **Bookmark**: review ref + saved-at. A shelf, not a verdict.
- **Todo**: text, severity, state (`open` | `done` | `parked`), order index. Supports check off, delete, reorder.
- **Posture**: per-review lane (`tried` | `parked` | `skipped` | `passed`). `skipped` ≠ `passed`.

Rebuild requirement: local persistence first (localStorage/IndexedDB), export/import
(JSON) so no review is ever lost in a rebuild. The 19 seed reviews must import cleanly.

## 4. Real vs stubbed (as of 2026-09-23)

- REAL: all 19 reviews and their verdicts, bookmarks, to-do items, card layouts, lab interactions.
- STUBBED / placeholder: none intentionally — if you find lorem or demo residue, quarantine it and flag to Jon; do not ship it.
- Known asset gap: the dark-mode OpenDesign GitHub screenshot was never captured; the light-mode file is explicitly marked `IGNORE — LIGHT mode (bad capture)`. Do not use it in the template hero until Jon approves a replacement.

## 5. Design tokens

- Master retheme: tonal grey + **Gradients 12** (see Pages root).
- Jon's default website taste: dark charcoal + grays with red or gold accents. Teal/aqua/purple read as "default website type" — avoid as defaults.
- Card Concepts lab is intentionally experimental — finishes (holographic, liquid glass) are studies, not the master look.

## 6. Wiring checklist (for the real rebuild)

- [ ] Seed import: all 19 reviews with verdicts + harvest notes intact
- [ ] Verdict actions: try / park / skip posture per review; Pass as a distinct action
- [ ] Bookmarks shelf: save/remove, persists
- [ ] To-do: add / check off / delete / reorder / severity / park
- [ ] Filter: by verdict, lane, tag (see Filter Lab experiment)
- [ ] Layouts: at least the master list + Card Swap and Cover Flow (see lab)
- [ ] Export/import JSON backup of everything
- [ ] Mobile: no sticky-toolbar freeze (see fix below)

## 7. Best workflow

1. Trinity/Ravyn research → bridge note to Cursor (goal + source link + what done means).
2. Cursor verifies on Vader, builds in repo, writes back. No commit/push until Jon reviews the diff (standing: branches only, no PRs unless Jon asks, never push/merge to `main` without explicit approval).
3. Mason gates QA before anything goes live.

## 8. Known fixes to carry forward

- **Mobile sticky toolbar freeze (2026-09-23):** iOS Safari froze the sticky `.toolbar` over cards during momentum scroll. Fix: `.toolbar { position: static; }` inside `@media (max-width: 720px)`; desktop keeps sticky glass. Branch `fix-mobile-toolbar-sticky` (unmerged — re-apply, don't assume it's in).
- **Muse app Preview anchor bug (2026-09-23, twice):** in-page section links did nothing inside the Muse app's preview pane (worked in normal browsers). Fix: intercept in-page anchor clicks and drive `scrollIntoView()`/`window.scrollTo()` directly; keep normal fragment behavior for standalone hosting. Any new in-page nav must use this routine.

## 9. GitHub repo + Pages presentation standard

**Standing rule (Jon, 2026-09-23):** whenever Trinity or Ravyn creates or sets
up a new repo, both follow this same template. Same look and feel every time.

- **Badges** at README top (build/pages status, license).
- **Dark-mode hero screenshot** of the live product (never a light-mode capture).
- **README structure:** what it is → live demo link → screenshots → tech/running locally → project map → changelog pointer.
- **`.nojekyll`** at repo root (Pages serves vendored/static assets correctly).
- **Pages config:** Pages serves from `main` (branch-only workflow — feature work never lands on `main` without Jon's explicit merge approval; branches have no preview URL, the Muse artifact is the preview).
- **Social preview image** (repo Settings → Social preview).
- **About links:** live Pages URL + one-line description.
- Status: Jon's current repo page setup is tentative — treat specifics above as the target, flag deviations to him.

## 10. Changelog

- **2026-09-23** — Blueprint created (docs/CURSOR-BLUEPRINT.md). Carries the repo-presentation template + rebuild brief. Template status: target spec; Jon's repo setup still tentative.
- **2026-09-23** — GodUI interaction lab merged to `main` (branch `godui-experiments`): six new experiments on the 19 reviews (Filter Lab, Review Digest, Priority Queue, Finish Lab, inertia dragging, card menus, mega-menu nav, floating quick-nav). Live at `/card-concepts/`. Muse-app Preview anchor bug fixed same day.
- **2026-09-23** — `/card-concepts/` subpath created on Pages (Card Swap + Cover Flow concepts).
- **2026-09-23** — Harvest branch `harvest-godui-keepers` (from `main`): Trinity reviewed Ravyn's GodUI favorites lab and harvested the approved keepers into `/card-concepts/` — facet filter popovers with per-option counts + Marks filter, multi-select project combobox (search, match highlight, pin-to-top), expanded card ••• menu (Promote/Favorite, Set-lane submenu, Copy link; 36–44px trigger), drag-to-reorder priority queue (grip + arrow fallback), workspace mega menu (Download/Load/Reset/Clear; bottom sheet on mobile), live stat facet pills, and Ravyn's wordmark+favicon as the single shared LinkLabz logo. Deliberately EXCLUDED: gold foil cards, Coast fling gallery, Band expanding lanes, cover carousel, gold "Card Swap" top-bar button.
- **2026-09-23** — Shared-logo decision: LinkLabz gets ONE logo for both boards — Ravyn's hand-drawn wordmark (Link off-white, Labz gold, chain-link i-dot, z-spark) + amber-circle chain-link favicon, vendored at `brand/`. A dark-on-light variant (`wordmark-charcoal.svg`) ships in her brand package — use it on light surfaces.
- **2026-09-23** — For Cursor's eventual Vader rebuild (approved interaction set): the harvested patterns above are the interaction set to rebuild — faceted popover filters, project combobox, card action menu, reorderable to-dos, workspace menu, stat pills. Standing rule: ALL review data (19 reviews, verdicts, harvest notes, bookmarks, to-dos, queue order) is preserved across imports/rebuilds — never drop or re-seed user data.
