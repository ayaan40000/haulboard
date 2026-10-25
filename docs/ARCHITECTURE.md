# HaulBoard Architecture

## System overview

HaulBoard is a weekly pipeline that ends in a static page. There is no server and no database behind the website — all the intelligence is in the Friday run; the page is just its published output.

```
Store FB/IG flyers ─┐
Retailer ads/Flipp ─┤
Coupon sources ─────┼─> Friday sweep ─> Price database ─> Compare + tables ─> Page build ─> Privacy scan ─> GitHub push ─> GitHub Pages
Free-food listings ─┘        (Pi browser)   (SQLite)                            (single HTML)                    (this repo)
```

## Components

### 1. Sweep (data in)
- Runs in a private browser on the owner's home connection (store pages are login- and bot-sensitive from datacenter IPs).
- Capture protocol is deliberately exhaustive: **every** post since the last run, **every** carousel image, and each page's Photos grid. Prices live inside flyer images, not post text — snippet-level checking has already missed a headline goat deal once (2026-10-24) and is treated as a known failure mode, never a shortcut.
- Every captured price records its **source post permalink**; the board links deals back to their flyer.

### 2. Price database (memory)
- SQLite database of every captured price — deals, coupon prices, and regular flyer prices. Expired prices are kept as history; they power future trend work (Roadmap Phase 4).
- Items are **canonical** (one entry per item regardless of brand); brand, pack size, and weight are attributes. Bulk containers are recorded with weight as quantity.
- Derived views: best price per item, per-store competitiveness, and the category price tables published on the board (unavailable shown as "—", never guessed).

### 3. Page (product)
- `index.html` — one self-contained file: inline CSS and JS, system fonts, no external requests (the data-URI favicon is the only exception predating `icon.svg`). Loads instantly, works on any connection, and is trivially cacheable.
- Design: "Market Ledger" — deep green `#14532d`, warm cream `#f6f3ec`, marigold `#d97706`, Georgia serif headings, mobile-first single column.
- PWA shell: `manifest.webmanifest` + `icon.svg` for Add to Home Screen; `sw.js` service worker using network-first for the page (fresh board whenever online, last board when offline) and cache-first for the icon/manifest.
- A second edition of the board exists as an in-app dashboard (private) with the full trip planner; the public page is the shareable edition. Roadmap Phase 1 ports the planner here and makes this edition primary.

### 4. Publish (distribution)
- GitHub Pages on a public repo, branch `main`, root folder, `.nojekyll` (files served as-is).
- Publishing is a snapshot push through the GitHub connector (see DEVELOPMENT.md). Each confirmed push records its commit SHA and exact file list in a local sync record, so later pushes can prove their base.
- GitHub Pages redeploys automatically after each push; the deploy is verified by loading the live URL, not by trusting the push result.

## Trust and tagging

- **VERIFIED** — seen on the store/org's own post, flyer, or page in the current run.
- **UNVERIFIED** — from an aggregator or secondary source (e.g. Kroger coupons, whose gallery is login-walled). Shown, but labelled.
- No price, date, or event is ever invented to fill a gap; gaps render as "—" or an explicit "no new flyer" note.

## Privacy boundary

The sweep runs on private infrastructure and sees private accounts. The published artifact is the opposite: public repo, so the pipeline enforces a hard boundary at publish time — a scan for personal names, home street, and phone patterns must pass before any push, and only "Stafford, TX 77477" plus public store addresses may appear. "My list" is browser-local storage on the reader's device and is never transmitted.
