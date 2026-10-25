# HaulBoard Roadmap

How HaulBoard grows: one step at a time, each step earned by clean weekly runs.
The standing rule from the operating runbook is **scope grows only after 2 clean weeks**
at the current scope — a "clean week" means the Friday run completed the full sweep,
filed every price, and published without a missed flyer or a corrected price.

Last updated: 2026-10-24

## Done

### Phase 0 — Live board (2026-10-24) ✅
- [x] Weekly verified deal sweep: store FB/IG flyers (every post, carousel, and Photos-grid image), big-retailer ads, coupons, free-food distributions
- [x] Price database with canonical items (brand is an attribute), source permalink recorded for every price
- [x] Price compare + price tables by category with best-price highlights and "—" for unavailable
- [x] In-app dashboard (Muse artifact) with the full "Plan my haul" trip planner
- [x] Public GitHub Pages edition: single-file page, public repo, Pages live at https://ayaan40000.github.io/haulboard/
- [x] Friday automation refreshes both editions and privacy-scans the public page before every push
- [x] Full deployment: web app manifest, app icon, service worker (offline board), `.nojekyll`

## Next

### Phase 1 — Full planner on the public page
*Goal: the phone web app does everything the in-app board does, so the GitHub edition becomes the primary board.*
- [ ] Port "Plan my haul" in full: Add buttons on every priced item, store-grouped list, quantity steppers, per-store subtotals and trip total
- [ ] Coupon/promo reminders per store inside the list
- [ ] Free-food stops addable to the trip at $0 with day/time windows
- [ ] Savings meter (list total vs. buying everything at the priciest store) and the one-stop pick
- [ ] Copy/print the list; keep the list saved on-device only
- [ ] Acceptance: a test list (goat $5.99 + drumsticks $0.79 + wings $1.29) totals exactly $8.07 with $1.70 savings, matching the in-app planner's verified arithmetic

### Phase 2 — Publishing hardening
*Goal: a bad publish becomes impossible, not just unlikely.*
- [ ] Automated checks on every push: HTML validity, internal anchor links resolve, no external resources, file-size budget
- [ ] Privacy scan as a blocking gate (name / street / phone patterns) — already run manually each Friday; make it a checked-in script
- [ ] One-command rollback to the previous Friday's page
- [ ] Deployment note appended to CHANGELOG.md automatically each run

## Later (in ladder order — each unlocks after 2 clean weeks)

### Phase 3 — More stores, then more categories
- [ ] Activate the discovery watchlist full-time: Zaina Supermarket, Prime Bazaar daily meat counter, Phoenicia, Awami, Texas Grocers, Simply Halal, Afgh Houston
- [ ] In-store price checks for The Meat Grinder Market (Richmond — no public flyers)
- [ ] New categories once meat/produce/staples are stable: frozen, dairy depth, baby/household essentials

### Phase 4 — Price trends and watch triggers
*Unlocks after 4 weeks of price history (earliest: late Nov 2026).*
- [ ] Per-item price spark-trends on the board (4-week median, this week vs. median)
- [ ] Watch triggers, from the runbook: goat under $6.50/lb, drumsticks under $1.00/lb, any staple ≥30% below its 4-week median
- [ ] STOCK-UP badges computed from cross-store medians on the public page (already on the in-app board)

### Phase 5 — Store scorecards
- [ ] Per-store scorecard: how often it is cheapest per category, flyer reliability, average basket cost
- [ ] "Worth the drive?" note combining distance (Stafford base) with this week's savings vs. the nearest alternative

### Phase 6 — Cart pre-builds
- [ ] One-tap retailer carts for the week's list (Walmart list first — cheapest option per item unless a brand is named)
- [ ] Shareable list link for the household that does not expose the underlying account

## Principles (do not trade away)

- **Verified or labelled.** Every price is VERIFIED from the store's own post/flyer/page, or tagged UNVERIFIED. Nothing is invented.
- **Privacy before publish.** Public repo: no personal names, home street, or phone numbers — scan gates every push.
- **One file, no dependencies.** The page stays a single self-contained HTML file (plus the small PWA files) so it loads fast, works offline, and never breaks because a CDN did.
- **The board is the product.** Features ship only if they help answer "where is it cheapest, and is it worth the stop?" before a trip.
