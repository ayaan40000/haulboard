# HaulBoard — Know before you go

Weekly grocery and meat deals near Stafford, TX 77477, verified from store flyers and retailer ads.

**Live site:** https://ayaan40000.github.io/haulboard/
**Repo:** https://github.com/ayaan40000/haulboard

## What it is

HaulBoard answers one question before a shopping trip: *where is each item cheapest this week, and is it worth the stop?* It is built for a halal household in Stafford, TX, and is updated every Friday.

Sections on the page:

- **Top picks** — the 5–8 best verified deals of the week, headline deal first
- **Store sections** — Al Aqsa Bangla Bazaar, World Food Warehouse, and More stores (Papa Mart, Desi Brothers, Big Basket Stafford, Al Madina, Jerusalem, Hala Market)
- **Big retailers** — H-E-B, Walmart (Sugar Land, Hwy 6), Aldi, Kroger weekly ads for 77477, plus Flipp
- **Price compare** — best price per item across stores, plus full price tables by category, rebuilt weekly from the price database
- **Coupons & promos** — H-E-B coupon gallery, Kroger digital coupons (aggregator listings tagged UNVERIFIED until seen in the app), Walmart Cash offers; Aldi has no coupon program
- **Free food near Stafford** — food distributions within ~10 miles in the next 10 days, re-verified each run
- **Store watchlist** — every store checked each week
- **My list** — a shopping list saved only on your own device (browser local storage, nothing is sent anywhere)

## How it updates

Every Friday (~9:37 AM CT) an automated run:

1. Sweeps each watchlist store's Facebook and Instagram flyers — every post, every carousel image, and the Photos grid, because prices live in the flyer images
2. Pulls the big retailers' weekly ads and coupon sources
3. Re-verifies free-food distributions
4. Files **every** captured price into the price database (deals, coupon prices, and regular flyer prices; expired prices stay as history)
5. Rebuilds the price-compare and category tables, refreshes this page, privacy-scans it, and pushes it to this repo — GitHub Pages then redeploys automatically
6. Posts a short Friday summary (best deals, coupon finds, free distributions) in the owner's chat

Every item is tagged **VERIFIED** (seen on the store/org's own post, flyer, or page that run) or **UNVERIFIED**. Prices are never invented.

## Phone web app

The site is a progressive web app: open the live link in your phone browser and choose **Add to Home Screen**. It launches full-screen, keeps the current board available offline, and refreshes to the new board each Friday after the push.

## Repo layout

| Path | Purpose |
| --- | --- |
| `index.html` | The board itself — one self-contained file (inline CSS/JS, no external fonts, scripts, or images) |
| `manifest.webmanifest` | Web app manifest (name, colors, icons) for Add to Home Screen |
| `icon.svg` | App icon |
| `sw.js` | Service worker — offline copy of the current board, network-first refresh |
| `.nojekyll` | Tells GitHub Pages to serve the files as-is (no Jekyll processing) |
| `README.md` | This file |
| `ROADMAP.md` | Planned development, in phases with acceptance criteria |
| `CHANGELOG.md` | What changed, per deployment |
| `docs/` | Architecture, weekly operations, and development guides |
| `data/` | The data behind the board, published weekly: price database as SQL dump + JSON, raw captures, price tables, dated coupon shortlists — see `data/README.md` |

**Roadmap in one line:** HaulBoard is growing from a grocery board into a **home shopping suite** — coupons engine, household essentials, apparel clearance, furniture and home deals, and clearance outlets, all on the same verified price tracking. Full plan: [ROADMAP.md](ROADMAP.md).

## Documentation

- [Architecture](docs/ARCHITECTURE.md) — how the pipeline and the page fit together
- [Operations](docs/OPERATIONS.md) — the Friday run, step by step, with the rules that keep it accurate
- [Development](docs/DEVELOPMENT.md) — working on the page locally, validation, privacy scan, and the publish flow

## Privacy rules (non-negotiable)

This repo is public because free GitHub Pages requires it. Therefore:

- No personal names, home addresses, or phone numbers are ever published here — only "Stafford, TX 77477" and public store addresses already shown on the board
- Every push is preceded by a privacy scan for name / street / phone-number patterns; a hit stops the push
- "My list" data never leaves the device it was created on

## Status

Live since 2026-10-24. See [ROADMAP.md](ROADMAP.md) for what is next and [CHANGELOG.md](CHANGELOG.md) for deployment history.
