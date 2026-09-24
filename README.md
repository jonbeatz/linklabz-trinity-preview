# ⚡ LinkLabz — Trinity Preview

> Trinity's reimagined build of Jon's **LinkLabz** link-review board — faceted
> filtering, a command palette, and a review workflow tuned for harvesting
> ideas worth stealing.

[![GitHub Pages](https://img.shields.io/github/deployments/jonbeatz/linklabz-trinity-preview?label=github%20pages)](https://jonbeatz.github.io/linklabz-trinity-preview/)
[![Last commit](https://img.shields.io/github/last-commit/jonbeatz/linklabz-trinity-preview)](https://github.com/jonbeatz/linklabz-trinity-preview/commits/main)
[![Repo size](https://img.shields.io/github/repo-size/jonbeatz/linklabz-trinity-preview)](https://github.com/jonbeatz/linklabz-trinity-preview)
![Static site](https://img.shields.io/badge/site-static%20html-blue)

**🚀 Live preview:** https://jonbeatz.github.io/linklabz-trinity-preview/

![LinkLabz preview](assets/screenshot.png?v=2)

## What's inside

- **Faceted filter bar** — one compact facet per dimension (Project, Lane,
  Hardware fit, Found by, Action). Each facet opens a searchable multi-select
  popover with count badges; active facets collapse to a single chip
  (`Project: reWavz ×`); `Clear all` resets everything.
- **Actionable empty states** — no dead ends. Every empty result offers the
  next action inline (Clear filters / Add this link).
- **Segmented controls** for Grid/List and other mutually-exclusive switches.
- **Letter-tile card anchors** so the grid scans without thumbnails.
- **Grouped ⌘K command palette** with keyboard-shortcut chips and a permanent
  top-bar search button.
- **Refined slide-over drawer** — sticky footer action, blurred backdrop,
  Escape to close, bottom-sheet treatment on mobile.
- **Full dataset preserved** — 19 reviews, 7 bookmarks, 6 spitballs, 2 to-dos.

## Tech stack

| Layer   | Choice                                                        |
| ------- | ------------------------------------------------------------- |
| Markup  | Single self-contained `index.html` (CSS + JS inlined)         |
| Runtime | None — opens straight in the browser, no build step           |
| Hosting | GitHub Pages, served from `main` (`.nojekyll`, no Jekyll pass)|
| Data    | JSON seeded in-page; LinkLabz lanes: Reviews / Bookmarks / Spitballs / To-do |

## Project structure

```text
linklabz-trinity-preview/
├── index.html          # the whole app — self-contained build
├── assets/
│   └── screenshot.png  # README hero shot
├── .nojekyll           # tell Pages to serve files as-is
└── README.md
```

## Workflow — branches, not overwrites

`main` always mirrors the latest approved build. Every change gets cut as a
**new branch** off `main` and previewed via GitHub Pages before it lands.
Nothing is silently replaced.

## Use this repo as a template

This README is the house pattern for Jon's preview repos. Copy the shape:

1. Title + one-line description of whose build it is and what changed.
2. Badge row: Pages deploy status, last commit, repo size, site type.
3. Live preview link, then a real screenshot (`assets/screenshot.png`).
4. "What's inside" — the feature list in plain language.
5. "Tech stack" — the table above; keep it honest and short.
6. "Project structure" — the tree, so the next person knows where things live.
7. "Workflow" — the branching rule, so `main` never gets quietly overwritten.
