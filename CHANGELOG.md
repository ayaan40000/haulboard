# Changelog

All notable deployments of the public HaulBoard page. Weekly content refreshes (new prices and deals) happen every Friday and are summarised in the commit message; this log tracks structural and feature changes.

## 2026-10-26 — App-style redesign (pending push and live verification)
- Rebuilt `index.html` from a single scrollable page into a view-based web app per `docs/UX-REDESIGN.md`: six views (This Week, Stores with per-store detail screens, Compare, Coupons, Free Food, My List), one visible at a time, with a fixed bottom tab bar on mobile and a top pill bar on desktop, a slim sticky header, and a hash router that keeps all ten legacy hashes, the store slugs, and Back/Forward working
- No content lost: every priced item from the previous page is present, with bundled bullets split into one deal row per item; compare tables unchanged (9 tables); watchlist moved into Stores; footer now at the bottom of This Week
- "Add to my list" extended from the headline deal to every priced deal row and coupon shortlist rows, with per-item Remove and a My List tab count badge — storage contract unchanged (`haulboard-list-v1`), as is the theme contract (`hb-theme`)
- Weekly data is now delimited by paired `VIEW` / `SECTION` / `STORE` / `DATA` comment markers so Friday runs swap data without touching the shell; service worker cache bumped to `haulboard-v3`; manifest unchanged

## 2026-10-25 — Data published + suite roadmap
- Price database and JSONs now published with the board in `data/`: SQL dump and JSON export of the full price database (212 prices, 161 items, 11 stores), raw `captures.jsonl`, and the generated price tables, with a `data/README.md` explaining the schema and why the binary `.db` itself is exported as text
- `ROADMAP.md` rewritten: HaulBoard's destination is a home shopping suite — Track A groceries, Track B coupons engine (image-first, couponer-led shortlists), Track C household essentials and drugstores, Track D apparel clearance, Track E furniture and home goods, Track F bodegas/warehouses/clearance outlets, plus cross-cutting video-sourced pricing, price trends and alerts, and the suite dashboard
- Operations doc: coupon step is now image-first with a defined shortlist format, and a video-sourced pricing protocol was added (pilot: The Meat Grinder Market)

## 2026-10-24 — Full deployment
- Added web app manifest (`manifest.webmanifest`), app icon (`icon.svg`), and a service worker (`sw.js`) so the board installs as a phone web app and the current board stays available offline
- Added `.nojekyll` so GitHub Pages serves the files exactly as pushed
- Documentation: expanded `README.md`, added `ROADMAP.md`, `CHANGELOG.md`, and `docs/` (architecture, operations, development)
- Footer on the page updated from "prototype / not published" to the live deployment, with links to the repo documentation
- "My list" section wording corrected — the full trip planner is Phase 1 of the roadmap

## 2026-10-24 — First board live
- First push to the public repo `ayaan40000/haulboard` (commit `b85c8105`): `index.html` + `README.md`
- GitHub Pages enabled by the repo owner; deployment #1 ("HaulBoard: first board") Active, site verified live at https://ayaan40000.github.io/haulboard/
- Board content: Top picks (headline — Al Aqsa Goat Mix Tray $5.99/lb Sat & Sun, $6.99/lb weekdays, Zabiha, no limit), Al Aqsa, World Food Warehouse, More stores, Big retailers, Price compare (79 prices filed), Coupons & promos, Free food near Stafford, Store watchlist
