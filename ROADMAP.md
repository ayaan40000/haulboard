# HaulBoard Roadmap — from grocery board to home shopping suite

**Vision.** HaulBoard started as a weekly grocery and meat board. Its destination is a
**home shopping suite**: one dashboard for everything the household buys — groceries,
household essentials, clothing, furniture, and clearance finds — with prices tracked over
time, deals verified from source images and videos, and a single place to decide
*what to buy, where, and whether this week is even the right week to buy it.*

Last updated: 2026-10-25

## How this roadmap works

- **Tracks** are product areas (groceries, coupons, apparel, …). Each track has phases.
- **Gates:** a track's scope grows only after **2 clean weeks** at its current scope —
  a clean week = the Friday run completed the sweep, filed every price, and published
  with no missed flyer and no corrected price. New tracks start as *pilots* (one store or
  one source) and graduate the same way.
- **Cross-cutting capabilities** (image parsing, video pricing, price history) are built
  once and reused by every track.

## Principles (do not trade away)

- **Verified or labelled.** Every price is VERIFIED from the store's own post, flyer,
  image, or video — or tagged UNVERIFIED. Nothing is invented, ever.
- **Images and videos are the source of truth.** Deal math lives in flyer images,
  couponer screenshots, and video price cards — never in post text or snippets.
  Where a couponer has already worked a deal out, **their math is preserved exactly**
  and credited, not recomputed.
- **Privacy before publish.** Public repo: no personal names, home street, or phone
  numbers; a scan gates every push.
- **One honest dashboard.** If a price is stale, the board says so. Silence about a
  store means "not checked", never "no deal".

---

## Track A — Groceries & meat (live)

### Phase A0 — Weekly board ✅ (2026-10-24)
- [x] Verified sweep of store FB/IG flyers (every post, carousel image, Photos grid), big-retailer ads, free-food listings
- [x] Price database (canonical items), price compare + category tables, in-app board + public GitHub Pages edition, Friday automation

### Phase A1 — Full planner on the public page
- [ ] Port "Plan my haul": Add on every priced item, store-grouped list, qty steppers, subtotals, trip total, coupon reminders, $0 free-food stops, savings meter, one-stop pick, copy/print
- [ ] Acceptance: test list goat $5.99 + drumsticks $0.79 + wings $1.29 totals exactly $8.07, savings $1.70

### Phase A2 — More stores and categories (after 2 clean weeks)
- [ ] Graduate discovery stores full-time: Zaina, Prime Bazaar daily counter, Phoenicia, Awami, Texas Grocers, Simply Halal, Afgh Houston
- [ ] Deeper categories: frozen, dairy, baby and household grocery-adjacent

## Track B — Coupons engine (current focus)

*Goal: stop listing coupon sources and start publishing a **shortlist of worked deals** —
the ones couponers have already identified and priced out.*

### Phase B1 — Image-first coupon research (in progress, Oct 2026)
- [x] Source map: H-E-B coupon gallery (public), Kroger digitals via aggregators (UNVERIFIED until in-app), Walmart Cash, Aldi = no program
- [ ] Weekly couponer sweep, **images parsed**: Dollar General coupon-community posts (that day's posts; deal math read from the images, posters' math preserved verbatim), Kroger Krazy / iHeartKroger, CVS and Walgreens deal communities/blogs, Target Circle roundups
- [ ] Coverage: **Dollar General, H-E-B, Kroger, Walgreens, CVS, Target** every week; Walmart, Dollar Tree, and warehouse clubs when something is genuinely notable
- [ ] Output format per deal: item + size, shelf price, exact coupons/offers to clip and where, rewards involved, final price (poster's math where community-sourced), validity dates, conditions, VERIFIED/UNVERIFIED
- [ ] Published deliverables per round: a **Top 5 overall** + per-store shortlist on the board's Coupons section, and a dated `data/coupons-YYYY-MM-DD.json` in this repo
- [ ] Standing highlight: Dollar General Saturday $5-off-$25 scenarios, worked out from community posts each week

### Phase B2 — Coupon tracking
- [ ] Clip-by dates surfaced as a "clip before Saturday" checklist
- [ ] Deal outcomes logged: did the shortlist price match at the register? (feeds source reliability scores)
- [ ] Expiring-digital alerts folded into the Friday summary (48-hour window)

## Track C — Household essentials & drugstores

- [ ] Walgreens/CVS/Walmart/Target household categories as first-class price-tracked items: paper goods, laundry, cleaning, health & beauty — same canonical-item database, new category trees
- [ ] Stock-up thresholds per essential (e.g. paper towel per-sheet, detergent per-load) so "cheap" is computed, not vibes
- [ ] Clearance endcaps and seasonal resets tracked per store

## Track D — Clothing & apparel clearance (future)

- [ ] Pilot: one source first — Target clearance + one off-price chain (TJ Maxx/Marshalls/Ross) near Stafford/Sugar Land
- [ ] Clearance cadence map: when each store marks down (day-of-week patterns, seasonal transitions), % off typical ranges
- [ ] Size/section-scoped tracking (men's, women's, kids) — headline markdowns only, no attempt at full catalogue coverage
- [ ] Graduate after 2 clean pilot rounds: add Walmart clearance and department-store sale events (Kohl's, JCPenney)

## Track E — Furniture & home goods (future)

- [ ] Pilot sources: IKEA Houston, Ashley/HomeGoods, Target/Walmart furniture sales, Costco/BJ's/Sam's rotations
- [ ] Big-ticket price tracking: watchlist of named items (sofa class, mattresses, dining sets) with price history, so a "sale" can be judged against the real baseline
- [ ] Floor-model / open-box / outlet deals as a labelled sub-stream (condition always stated)
- [ ] Delivery and assembly costs included in comparisons — sticker price alone is not the deal

## Track F — Bodegas, warehouses & clearance outlets (future)

- [ ] Map and pilot the local clearance ecosystem: bodegas and discount grocers around Stafford/Houston, warehouse sales, liquidation and bin stores, salvage/closeout outlets
- [ ] In-store-only pricing protocol (photo captures on a visit list; no flyer assumed)
- [ ] Quality/condition grading rule for salvage goods (sealed/open, expiry windows for food) before any deal is published

## Cross-cutting capability 1 — Video-sourced pricing

*Some stores publish prices only in video (e.g. The Meat Grinder Market, Richmond —
walkthroughs/reels instead of flyers). The suite needs ears and eyes, not just OCR.*

- [ ] Pilot protocol (Meat Grinder Market): inventory their video posts (platform, cadence); extract frames at price cards and scene changes; OCR on-screen price text; transcribe audio (price call-outs) with speech-to-text; reconcile frame vs. audio; each price filed with source = video URL + timestamp in the video
- [ ] Confidence rule: a price needs frame **or** clear audio; both agreeing = VERIFIED, one source only = tagged with its source, garbled = not published
- [ ] Reuse for any store that goes video-first, and for reel-format flyers in Track A

## Cross-cutting capability 2 — Price history, trends, alerts

*Unlocks at 4 weeks of database history (late Nov 2026) — the database and its exports now live in `data/`.*

- [ ] Per-item trends: this week vs. 4-week median, spark-trends on the board
- [ ] Watch triggers: goat < $6.50/lb, drumsticks < $1.00/lb, any tracked staple ≥30% below its 4-week median (generalises to essentials/apparel staples per track)
- [ ] STOCK-UP badges computed from medians on the public page (already on the in-app board)

## Cross-cutting capability 3 — The suite dashboard

- [ ] One home view over all tracks: this week's best deals per category, price-drop alerts, clip-by checklist, and per-track freshness ("groceries checked Fri; apparel pilot checked Tue")
- [ ] Store scorecards: cheapest-per-category frequency, flyer/video reliability, basket cost, "worth the drive?" from Stafford
- [ ] Cart pre-builds: one-tap retailer lists (Walmart first — cheapest option per item unless a brand is named)
- [ ] Household sharing that exposes no account data (public page + on-device lists, as today)

## Data foundation (done, maintained weekly)

- [x] Database + JSONs published with the board in `data/` (SQL dump, JSON export, raw captures, tables) — text formats, diffable in git; see `data/README.md`
- [ ] Category taxonomy v2 when Track C starts (home essentials tree) — canonical-item rules unchanged
