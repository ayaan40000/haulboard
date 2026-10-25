# HaulBoard data

The data behind the board, published with it every week. All files are plain text (UTF-8) so they can be browsed, diffed, and reused.

| File | What it is |
| --- | --- |
| `prices.sql` | Full SQL dump of the price database. Rebuild the database with `sqlite3 prices.db < prices.sql` |
| `prices.json` | The same database as JSON: `{ "stores": [...], "items": [...], "prices": [...] }` |
| `captures.jsonl` | Raw capture log — one JSON object per observed price, exactly as recorded from the flyer/ad, before canonicalisation |
| `tables.json` | Full cross-store price tables generated from the database |
| `tables_page.json` | The trimmed tables actually rendered in the board's Price tables section |
| `coupons-YYYY-MM-DD.json` | Dated coupon shortlists (added per research round) — couponer-identified deals with the posters' math preserved, tagged VERIFIED / UNVERIFIED |

## Why there is no `prices.db` file here

The live database is SQLite (binary). The publishing flow for this repo accepts validated text snapshots only, so the database is published as a SQL dump plus a JSON export instead — both rebuild the exact same database (212 prices, 161 canonical items, 11 stores as of 2026-10-24), and text diffs make weekly changes reviewable in git history.

## Schema (summary)

- **stores** — one row per store (name, city/area)
- **items** — canonical items (one entry per item regardless of brand; brand, pack size, weight are attributes), with category
- **prices** — observations: item, store, price, basis (per lb / each / pack), validity window, coupon note where relevant, source (flyer post / ad, with permalink recorded in the working files), and a verified flag

Expired prices are kept — they are the history the price-trend features (Roadmap Phase 4) are built from.

## Rules

- Every price traces to a source: a store's own flyer post/ad, or a labelled UNVERIFIED secondary source
- No personal data is stored in these files — no names, no home address, no phone numbers; locations are store addresses and "Stafford, TX 77477"
- Updated on the Friday run, in the same commit as the refreshed board whenever data changed
