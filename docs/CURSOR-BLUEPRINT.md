# CURSOR-BLUEPRINT.md — LinkLabz (Trinity preview)

> **LIVING DOC — update this file with every repo revision (date + branch + what changed).**
> Keep the CHANGELOG below current. Nothing in this doc is a build order until Jon gives the go.

## CHANGELOG

- **2026-09-23 — branch `gradient-lab-upgrade`:** Interactive Gradient Lab added to the Gradients tab (palette picker fed by the 12 gallery cards, 2 custom hex inputs, linear/radial/conic types, angle slider, shuffle, live preview, Copy CSS / Copy SVG / Download SVG, Harvest-to-spitball on the lab and on every gallery card via the existing in-memory `spitballs` + `renderUtilities()` + `markDirty()` flow). Contrast/accessibility pass: stat-value accents verified ≥ 5.05:1 (AA), `.contrast-rating` labels moved from `--muted` to `--ink`, lab controls are native focusable elements with labels and an `aria-live` preview caption. Counts preserved: 19 reviews, 7 bookmarks, 6 spitballs, 2 to-dos. No saturated colors anywhere.
- **2026-09-23 — branch `card-concepts`:** Added `card-concepts/index.html` — self-contained static export of the Muse artifact `linklabz-card-concepts` ("LinkLabz Card Concepts"): Card Swap stacked-deck and Cover Flow 3D carousel concept demos rendering all 19 LinkLabz review cards, dark mode default, working section navigation + back-to-top, project overview write-ups, per-layout "best use" notes, a verdict reference key, and help lightboxes. Root `index.html` and all existing files untouched. `card-concepts/README.md` notes what the page is. Not live on GitHub Pages until this branch is merged to `main` (Pages serves `main`; the page will live at `/card-concepts/` once merged).
- *(Add the next revision here.)*

## 1. App map

Single self-contained `index.html` (inline `<style>`, inline body markup, one inline `<script>` IIFE). No build step, no backend. Seven workspace tabs in the top nav, each a `<section data-panel="…">` toggled by `showWorkspace(name)` (sets `hidden`, toggles the active class, re-renders utilities). On ≤720px the tab strip collapses behind a hamburger (`nav-toggle`) with a scrim.

| Tab | What it does |
|---|---|
| **Reviews** | The core board. Two view modes: **lanes** (Inbox / Try it / Parked / Skipped columns, default) and **grid** (flat list). Search, category filter, sort, and a five-facet filter bar (project, lane, hardware, found-by, action). Clicking a card opens the detail drawer. |
| **Bookmarks** | Lightweight bookmark shelf ("Bookmark this" saves here too). Rendered as utility cards by `renderUtilities()`. |
| **Spitballs** | Idea parking lot. Same utility-card rendering. The Gradients lab's **Harvest to spitball** appends here. |
| **To-do** | Personal action tracker with check-off / delete / reorder, severity levels, and parking for far-future ideas. Supports to-dos linked back from a review (`+ Linked to-do` in the drawer). |
| **Favorites** | Derived tab — aggregates anything starred across reviews/bookmarks/spitballs. |
| **Gradients 12** | Reference gallery of 12 hand-authored tonal-neutral gradient cards (swatch, name, Copy CSS, 3 stops with clickable hexes and contrast-rating labels), credit line to `feralui.dev/gradients`, plus the **Gradient Lab** (see below). |
| **Tools** | Re-review queue (items with `revisitOn`), live stats, project-vocabulary rename/merge across all four collections, session activity log (60 entries max). Tab badge shows the re-review count. |

Supporting surfaces:

- **Detail drawer** — `#modal` (modal-backdrop) containing `<aside class="drawer" role="dialog">`. `openModal(id, trigger)` populates ~25 fields from the review object (verdict chip, title, why, rating stars, grade, harvest list, files, recommendation, evidence checklist, related items, project/category/hardware/found-by/revisit metadata, reference screenshot, optional Cursor-brief preview, and a site-preview `<iframe>` shown only for design/website categories). The drawer is also the live edit surface: rating stars, lane pills, action select, project select, Favorite, Promote, Delete (seed-blocked), `+ Linked to-do`, Copy Markdown, Copy bridge note, screenshot paste/drop (stored as data URL, in-session only), Drive-comp URL field, evidence checkboxes. Every mutation touches the item, re-renders, and marks the session dirty. **HOLD semantics:** the MiniMax H3 review carries `hold: true` + `holdReason`; hold blocks lane changes, action changes, and promotion with a toast ("HOLD blocks … until the grant email is reviewed.").
- **⌘K command palette** — `openCommand()`: searches/acts across all collections (navigation, add review/bookmark/task, download workspace, filters, lane changes for the open card, jump to individual items). Esc closes every modal.
- **Gradient Lab** — client-side editor inside the Gradients tab, above the gallery. Controls: palette source `<select>` (populated from the 12 gallery cards at init), 2 custom hex `<input>`s (start/end), type `<select>` (linear/radial/conic), angle `<input type="range">` (linear only), Shuffle button, live preview with an `aria-live` CSS caption. Outputs: Copy CSS, Copy SVG, Download SVG (Blob → `a[download]`), Harvest to spitball.

## 2. Data model

**Counts (shipped):** 19 reviews · 7 bookmarks · 6 spitballs · 2 to-dos.

**Shapes (observed in the code, schema v3 via `ensureSchema()`):**

```js
// review (reviews are deep-frozen as seededReviews; restoreSeededReviews()
// re-inserts any deleted seed on render; delete is disabled for seed cards)
{
  id, title, url, date, verdict,        // verdict: "try" | "park" | "skip" | "hold" …
  stage, project, projects[],           // project: display string; projects[]: vocab array
  category, categoryLabel, rating,      // rating: number (e.g. 4.8)
  gradeSummary, why, harvest[],         // harvest[]: cherry-pickable ideas
  files[], recommendation, brief,       // brief: Cursor brief text (String.raw blobs)
  lane, action, favorite, promoted, done,
  hold, holdReason,                     // MiniMax H3 only
  inboxVerdict, hardwareFit[], revisitOn, lastReviewed, foundBy,
  relatedIds[], evidence[], createdAt, updatedAt, schemaVersion
}
// bookmark — same base shape; has url, type ("Design"|"Reference"|…), notes, no verdict/stage
// spitball — same base shape; type "Idea", priority ("soon"|"later"|…), notes
// to-do    — same base shape; type "Task", priority, notes
```

**Persistence — READ THIS CAREFULLY:** there is **no localStorage, no sessionStorage, no IndexedDB** anywhere in the build (verified by token scan). All data lives in embedded JS literals (`var reviews/bookmarks/spitballs/todos`). Edits mutate the in-memory arrays and set a dirty flag (`markDirty`/`markClean` → "Unsaved changes · download to keep"); `beforeunload` warns on unsaved changes. The ONLY persistence path is the manual **Download workspace** JSON export in the managebar (`workspacePayload()`, format version 3) and a manual file-upload **import** with upsert merge + automatic timestamped pre-import backup. `harvestGradient()` follows the same rule: `ensureSchema({...})` → `spitballs.push(item)` → `renderUtilities()` → `markDirty("Gradient harvested", title)` → toast. It does not invent a new storage scheme.

## 3. Real vs stubbed

**Genuinely functional (static, client-side):** all tab rendering, lanes/grid views, search + 5-facet filtering, sorting, the ⌘K palette, the full drawer edit surface (in-session), HOLD gating, favorites/promote/delete flows, Copy CSS/hex/SVG (clipboard API + fallback), Download SVG, Harvest to spitball (in-session), JSON workspace export/import, project-vocabulary rename/merge, activity log.

**Placeholder / session-only:** cross-visit persistence (manual download only — nothing auto-saves); the site-preview iframe (external URL, only for design/website categories); the Drive-comp URL field; screenshot paste/drop (data URL, dies with the session); the hand-written AA/AAA contrast-rating labels on gradient stops (assertions, not computed); tab badge hard-coded labels in markup (corrected by `renderUtilities()` on load).

## 4. Wiring checklist for the real build (stack-agnostic — Cursor decides)

- [ ] **Persistence upgrade.** Keep the JSON schema v3 field shape as the contract so existing workspace exports import cleanly. Candidates: keep the export/import model and add auto-save to a chosen store, or move to a real database. Do not silently change field names.
- [ ] **Harvest-to-spitball loop.** Currently in-session. Real build should persist the harvest, optionally auto-suggest a project tag, and confirm the new spitball is visible in the Spitballs tab.
- [ ] **Gradient lab.** Already fully client-side (no server needed). Upgrades to consider: saved palettes, PNG export, mesh-gradient type, per-stop position sliders.
- [ ] **Seed protection.** Keep the deep-frozen `seededReviews` + `restoreSeededReviews()` + seed-delete-disable behavior until Jon says otherwise — it's what keeps the shipped 19-card catalog immutable in preview.
- [ ] **HOLD semantics.** Port the `hold`/`holdReason` blocking behavior (lane/action/promote) to whatever the new mutation layer is.

## 5. Design tokens

Full `:root` token set (light mode; a `prefers-color-scheme: dark` block mirrors these):

```css
--bg: #e8e5e1;            --bg-deep: #d9d5d0;
--surface: rgba(249,247,244,.88);   --surface-solid: #f3f1ee;
--ink: #0d0d0d;           --muted: #5a544b;
--line: rgba(28,28,28,.14);         --line-strong: rgba(28,28,28,.28);
--accent: #524748;        --accent-strong: #36363b;
--try: #5b6f57;           --parked: #5a544b;
--skipped: #6b6968;       --cyan: #6a6070;   /* legacy name — it's a grey */
--danger: #524748;        /* tonal crimson-grey, NOT a saturated red */
```

Neutral family used by the 12 gradients: `#0D0D0D` (Black) · `#1C1C1C` (Ink Black) · `#36363B` (Amakusa Black) · `#524748` (Quenched Crimson Grey) · `#524E4D` (Charcoal) · `#5A544B` (Sooty Bamboo) · `#6A6070` (Dusk Temple Grey) · `#5B6F57` (Rusted Storeroom).

**Rule: no saturated red / teal / cyan / yellow anywhere in the visible UI.** The old `--cyan`/`--danger` token names are legacy — they now hold muted greys. Contrast floor verified 2026-09-23: stat-value accents (sage `#5b6f57` 5.05:1, mauve `#6a6070` 5.53:1, taupe `#5a544b` 6.95:1 on ≈`#f8f6f4`) all pass WCAG AA; `.contrast-rating` labels render in `--ink`.

## 6. Best workflow for Cursor (Vader)

1. Clone the repo and open `index.html` directly in a browser — no build, no server.
2. Verify in this order: (a) all 7 tabs render and switch; (b) Reviews lanes + grid + 5-facet filters + search; (c) open a review drawer, change a lane, confirm the dirty flag ("Unsaved changes") appears; (d) Download workspace → JSON downloads; re-import it → upsert works; (e) Gradients tab: lab generates, Copy CSS/SVG + Download SVG work, Harvest to spitball lands a new card in Spitballs; (f) ⌘K palette opens and jumps; (g) HOLD card (MiniMax H3) refuses lane/action changes with the toast.
3. Confirm counts: 19 reviews · 7 bookmarks · 6 spitballs · 2 to-dos (before any harvest).
4. Check both color schemes: light default + OS dark mode (`prefers-color-scheme`).
5. Mobile ≤720px: hamburger tab strip, drawer becomes a bottom sheet.
6. When changing anything: cut a **new branch** off `main`, never push to `main` directly; update this doc's CHANGELOG.

## 7. GitHub repo + Pages presentation standard — OPEN

*Flagged OPEN 2026-09-23: Jon says the current repo page setup is tentative and could be better. This is the checklist both LinkLabz mirror repos (`linklabz-trinity-preview`, `linklabz-forge-preview`) should converge on — not yet done.*

- [ ] **README badges:** live-demo (Pages deploy status), license, last-updated/last-commit, repo size. Keep them honest and working.
- [ ] **Hero screenshot:** dark-mode capture of the app as the README hero (`assets/screenshot.png`). All repo-page screenshots in dark mode.
- [ ] **README structure:** title + one-line description → badge row → live preview link → hero screenshot → "What's inside" feature list (plain language) → tech-stack table → project structure tree → data note (counts + persistence model) → branch workflow (new branch per revision, never push `main`).
- [ ] **`.nojekyll`** at repo root so Pages serves files as-is.
- [ ] **Pages source config:** Pages serves from `main` (root). Branch previews are not live until merged — say so in the README if a branch is under review.
- [ ] **Social preview image:** set a repo social preview (dark-mode hero, 1280×640).
- [ ] **About section:** website = Pages URL, topics/tags set, description one-liner.
