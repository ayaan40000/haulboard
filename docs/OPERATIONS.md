# HaulBoard Operations — the Friday run

The board refreshes every Friday at ~9:37 AM CT. This document is the operator's summary; the full, detailed runbook (per-store playbook, cadences, failure notes) is maintained privately alongside the price database and is read at the start of every run — it gets smarter every week, and anything learned in a run is appended to it.

## The run, step by step

1. **Sweep** — open the Facebook and Instagram pages of every watchlist store in the Pi browser (serially — one browser at a time). Open every post since the last run, every carousel image, and the Photos grid. Screenshot reel frames where price text is on screen. Record each price with its source post permalink and sale dates. Also pull Prime Bazaar's daily meat-counter page and check for Meat Grinder Market flyers (in-store prices only so far).
2. **Coupons** — H-E-B's public coupon gallery; Kroger digital coupons via aggregator transcriptions, tagged UNVERIFIED (the gallery itself is login-walled; clip location is the Kroger app); Walmart is Walmart Cash app offers (no gallery exists); Aldi has **no** coupon program — never list Aldi coupons. Scan the coupon-community groups named in the runbook for deal-with-coupon posts.
3. **Big retailers** — H-E-B, Walmart (Sugar Land Hwy 6), Aldi, Kroger weekly ads for ZIP 77477, via store ad pages and Flipp. Meat per-lb prices first (goat, lamb, beef, chicken), then staples.
4. **Freebies** — free food/essential distributions within ~10 miles of Stafford 77477 in the next 10 days, re-verified against the organisations' own pages every run (schedules change). Standing starting points include East Fort Bend Human Needs Ministry (Stafford), India House Houston, Mamie George Community Center (Richmond), Second Mile Mission Center (call first — pickup schedule not published), ICNA Relief, CCSC, and the Houston Food Bank partner locator.
5. **Festival pantry** — when a festival sale is live (Diwali, Ramadan, Eid), maintain the festival section: non-produce, non-meat deals (grains, dals, rice, flours, oils, ghee, spices, sweets) with cross-store comparisons.
6. **File** — every captured price goes into the price database (canonical items; "with coupon" noted where relevant). Expired prices stay as history.
7. **Build** — regenerate the comparisons and category tables, rebuild both editions of the board: the in-app dashboard and this repo's `index.html` from the same week's data.
8. **Scan + publish** — privacy-scan the public page (names / street / phone patterns). Any hit stops the push. Push the snapshot, record the commit, verify by read-back, then verify the live Pages URL after redeploy.
9. **Summarise** — post the Friday summary: 5–8 best verified deals with prices and end dates, best coupon finds, free distributions in the next 7 days, and the links. Silence is not an option on Fridays — a summary always posts, even if a step failed, saying so plainly.

## Rules that keep it accurate

- Never report "no deal" from post text or search snippets — the flyer images are the source of truth.
- Keep only deals valid for the coming weekend/week on the board; expired ones go to history.
- One browser agent at a time; store pages are the bottleneck and are treated serially.
- A failed or blocked step is reported as failed — never papered over with last week's data presented as new.

## Failure handling

| Failure | Response |
| --- | --- |
| Store page unreachable / bot check | Note it in the summary, carry the store as "not checked this week", retry next run — never guess prices |
| Privacy scan hit | Stop the public push, fix the content, re-scan. The in-app edition still updates |
| GitHub push blocked or unconfirmed | Report it as not published; reconcile the repo state before any retry (a first push may have landed) |
| Pages deploy stale | Verify via the live URL; a push is not "live" until the page itself loads with the new date |
