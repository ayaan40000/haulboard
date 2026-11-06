# Changelog

All notable deployments of the public HaulBoard page. Weekly content refreshes (new prices and deals) happen every Friday and are summarised in the commit message; this log tracks structural and feature changes.

## 2026-11-06 — Friday refresh: Add buttons fixed to the contract format, stale prices closed
- Every Add button on the board now carries the full contract format `"<Item> — <Store> — <Price>"` (previously most rows carried item — flyer name with no price or store, so Plan my haul could not price them). The planner now reads name, store and price from every row
- Data trust: undated flyer prices are now closed when a later flyer supersedes them, so a stale price can no longer show as current. This run closed Papa Mart's Oct 17 Meat Deal flyer (valid to Oct 18, the Fri–Sun pattern) and Prime Bazaar's Oct 30 Diwali promo rows (superseded by the Nov 6 re-verification at $15.49). The sample basket total corrected from $109.10 to $117.28 once the stale Papa Mart chicken prices stopped dragging it down; the master compare table narrowed 34 → 23 contested items for the same reason
- New structured data file `data/coupons-dg-2026-11-06.json` (7 DG baskets: TheFreebieGuy Saturday scenarios #1 and #7, DG Diva household and cold & flu, Sarah CK Jones weekday personal-care and Advil/Aussie/AXE, Kisha Twins Gain) and the coupon rebuild updated to it; both header dates updated to Nov 6 per the standing rule
- Content: Al Aqsa Weekend 3-Day Sale (Nov 6–8, hilsa/rohu), Big Basket Weekly Sale (Nov 5–8), Prime Bazaar counter re-verified (leg quarters 40 lb box $29.99), Papa Mart Nurpur launch, Meat Grinder ice-cream reel prices, 18-coupon shortlist, 51 pre-trip events, 664 prices filed

## 2026-11-05 — Coupons view: item tables + store-app links (owner request)
- Every basket in "Best per store" is now laid out as an item table (Qty | Item | Each) instead of a run-on sentence in the row title; the row title is now just the basket name. All 20 rows rebuilt across Dollar General, CVS, Walgreens, Kroger, H-E-B, Target and Walmart; deal meta, prices, poster math, Add buttons and the DG photo strips are unchanged
- Each item line shows its exact app search query as a link ("Search: <item> ↗") built from the store's own search URL — on the phone these open the store app, so a basket cart can be built item by item. DG pattern verified live on dollargeneral.com (`/product-search?q=…&productSearchedMode=manual`); Target and Kroger patterns verified against live search results; Walmart, H-E-B, CVS and Walgreens use their long-standing search URLs. The penny list's 17 items are now a full table with UPCs
- Coupons are now chips under each row ("Clip in the DG app: …"), each linking to that store's coupon gallery (DG coupons.dollargeneral.com, Kroger Savings, heb.com/coupons, CVS, Walgreens offers, Target Circle); every "Clip this week" checklist row also gained an "Open in <store> app" link (the combined CVS/Walgreens row carries both)
- Format is standing: the Friday run rebuilds coupon rows in this layout (link templates registered in `docs/DEVELOPMENT.md` and the workspace RUNBOOK standing push rules)
- Service worker cache bumped to `haulboard-v6`; both header dates updated to Nov 5 per the standing rule

## 2026-11-04 — New tab: Events
- New **Events** view (seventh tab, between Free Food and My List; hash `#events`): family events within ~30 minutes' drive of Stafford 77477 for two adults and a 4-year-old, each row with date/times, venue, drive minutes, cost, a toddler-fit note, and a VERIFIED / UNVERIFIED tag under the board's usual rule (organiser's or venue's own page, or the missing detail is named)
- First sweep (researched Nov 4): the pre-trip window Thu Nov 5 – Tue Nov 10 — Children's Museum Free Family Night + Diwali Celebration, BAPS Stafford Diwali festival, The Halal Social in Rosenberg (Sat Nov 7, 3–11 PM, free admission), Houston AfriFEST, Sugar Land Arts & Music Fest, library story time / toddler dance party / Aztec Dance and Drum, Imperial Park farmers market, Sugar Plum Market, Raindrop Lifestyle Festival, Vadtal Dham Diwali — plus a December section (Zoo Lights, Texas Halal Fest Dec 12 watch, Discovery Green cheap-skate Mondays, The Grinch movie night) and a sold-out warning for the Dec 1 Zoo free day
- Travel blackouts honoured from the first run: the family is in Belleville Nov 11–30, so that window's heavyweights (Fort Bend Diwali, Big Bounce, Sugar Land Tree Lighting) appear once in a "skipped" note, never as options
- Standing format from here: refreshed every Friday with the roundup, rolling 21-day lookahead, expired events drop off, structured data in `data/events-2026-11-04.json`, rows live between `SECTION: events-list` markers inside the frozen shell
- Service worker cache bumped to `haulboard-v5` (shell change); both header dates updated to Nov 4 per the standing rule
- Same-evening completeness sweep (owner request): Facebook Events discovery (1,000+ listings scanned via the connector and the Pi browser), organiser Facebook event listings, Eventbrite, AllEvents, HappeningNext, Houston Moms / Mommy Poppins roundups, the full Fort Bend library calendar and venue calendars — 22 more events added (Asia Society Night Market, Craft Community Day, Calvary and St. Theresa fall festivals, a Kids 1K fun run, 11 more library toddler sessions, Sugar Land Holiday Lights, a December Santa-events set), two corrections applied (Sugar Plum Market: venue-confirmed hours, strollers/carts/wagons banned inside, price reports corrected to $12–$15; The Grinch movie night upgraded to VERIFIED and free), five more trip-window skips named, and two unresolvable finds (a Gwalia Diwali mela post with no venue, the Hollydays Market of Rosenberg with no venue stated) held off the board with the reason stated in the tab footer

## 2026-11-03 — Mid-week Dollar General update (Facebook group posts)
- Coupons view: the expired Oct 31 Saturday DG rows are replaced by this week's DG deals from the Couponing For Beginners And Experts Facebook group (posted Tue Nov 3): four Sarah CK Jones "Weekday Savings" baskets for Mon–Fri Nov 2–6 — personal-care $25.25 → $4.25, cleaning $42.90 → $22.90, paper & dish $35.00 → $20.00, Household Essentials Bundle $37.80 → $20.30, all before tax with the poster's math verbatim and each deal graphic read in full (VERIFIED) — plus Kisha Twins Journey's any-day basket (Gain + 3 Colgate + Dawn, $28.95 → $14.00, valid any day to Sat Nov 7) and Coupon Lady's Tuesday Nov 3 penny list (17 items at $0.01 with UPCs; Halloween seasonal 50% off / candy 25% off markdowns)
- This week's DG $5 off $25 is a weekday coupon (Mon–Fri), not Saturday-only; the clip checklist is retitled "Clip this week" and its DG rows now list the digitals behind the new baskets plus the penny-list app-scan step. Other stores' rows unchanged (Friday run refreshes them)
- New data file `data/coupons-dg-2026-11-03.json` holds the six DG deals in structured form, including the full penny list with UPCs and source permalinks
- `DATA: plan` regenerated (stamp 2026-11-03; coupons 16 → 20, free food 7) so the clip checklist resets for the new week
- Product photos added to the six DG deal rows (owner request, same evening): a thumbnail strip under each basket showing every product in it, hotlinked from public retailer/manufacturer CDNs (Walmart, Amazon, CVS, Kroger, Albertsons and others) — every URL preflighted 200/image before publishing, and each image self-removes if its source ever breaks. Penny row shows the five findable penny products (sunscreens, Raid stick/ribbon, Spectracide, Pop-Tarts); the Bugables wristband had no usable public image. Image URLs are also recorded per item in `data/coupons-dg-2026-11-03.json`, and the images themselves are saved in the workspace image library for reuse
- Header date corrected to As of 2026-11-03; from now on every push updates the header date to the push date (standing rule)
- Second date string caught on review: the top-right week-stamp also read "As of Oct 31" — now "As of Nov 3"; the standing rule covers both date strings

## 2026-10-30 — v4: full planner (Phase A1) + clip-by checklist (Phase B2)
- My List is now "Plan my haul": deal rows group by store with per-store subtotals, quantity steppers, a trip total, a savings meter (each item vs the next-best listed price for the same item, from the new `DATA: plan` block generated by `plan_data.py`), a fewest-stops pick, Copy list and Print. Acceptance test passes: goat $5.99 + drumsticks $0.79 + wings $1.29 totals exactly $8.07 with $1.70 savings. List storage contract unchanged (`haulboard-list-v1`); quantities live in a companion key (`haulboard-list-qty-v1`) so saved lists survive
- Coupons view gains a "Clip before Saturday" checklist (`SECTION: clip-checklist`): the digitals behind the week's deals, ticked off in the store apps; checks persist on-device (`hb-clip-v1`, reset each week by the DATA stamp)
- Same-day research re-run: H-E-B coupon gallery pulled from heb.com (32 VERIFIED coupons — the morning run had none), produce gap filled from the Wed–Tue retailer ads, free-food list re-verified (ICNA Relief Houston moved to 5645 Hillcroft Ste 405, registration/by-appointment only)
- Service worker cache bumped to `haulboard-v4` (shell change)

## 2026-10-30 — Weekly refresh (Oct 30)
- New verified prices: Al Aqsa Everyday Sale rices (Jazaa Basmati 10 lb $11.99, Palli Kalijeera 10 lb $15.99, Chinigura 10 lb $20.99, Par Excellence Parboiled 25 lb $18.99), Hala Market cuts ($5.99/lb) and lamb leg/shoulder ($8.99/lb), Meat Grinder fruit $0.99/lb Fri–Sun, Prime Bazaar counter prices (leg quarters 40 lb box $33.49, chicken breast/thigh 5 lb $15 Diwali Promo)
- Big-retailer ad transcriptions for Oct 28–Nov 3 marked UNVERIFIED (H-E-B, Kroger, Aldi, Walmart); stores with no new flyer verified this run (World Food Warehouse, Desi Brothers, Al Madina) are marked as such instead of being repriced from last week
- Coupons shortlist for Oct 30: Dollar General Oct 31 Saturday scenarios on top (P&G basket $9.70 + $10 next trip; household basket $12.45), plus Kroger, CVS, Walgreens, Target and Walmart Cash finds
- Access note: the pib SSH channel broke again this run, so the Pi-browser sweep used the CDP fallback; existing browser tabs were left untouched
- Price database now holds 264 prices; data exports (prices.sql, prices.json, captures.jsonl, tables.json, tables_page.json, coupons-2026-10-30.json) republished with the board

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
