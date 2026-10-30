# HaulBoard — UX Redesign Spec: from scroll page to web app

**Status:** Builder-ready design spec. A builder agent implements `index.html` from this
document alone, without further design questions. Where this spec conflicts with an older
doc, this spec wins for layout and navigation, and the conflict is flagged in §2.
**Product:** HaulBoard — "Know before you go." Weekly grocery and meat deals near
Stafford, TX 77477. One self-contained HTML file, live on GitHub Pages.
**Identity (unchanged):** "Market Ledger" — cream paper, deep green, marigold, Georgia
serif display type. This is HaulBoard's own identity. Do **not** import the sibling app's
Meridian Dark theme, components, or layout; the sibling's design doc was used only as a
model for how to structure an app (views, density, states), not for how HaulBoard looks.
**The problem this fixes (owner's words):** "Make a comprehensive and usable web app.
Not just a single scrollable page, it's neverending and the elements are not mobile
friendly at all." Today every section is mounted on one very long page — hero, headline
deal, top picks, four store sections, price compare with 9 tables, coupons, free food,
watchlist, my list — reached by anchor links. The redesign replaces that with discrete
views switched by a tab bar. One view visible at a time. Nothing is deleted: §4 maps
every current section to its new home, and the builder must be able to account for every
priced item on the current page after the rebuild.

---

## 1. Hard constraints (read first — these bound everything below)

1. **Single self-contained HTML file.** `index.html` keeps inline `<style>` and inline
   `<script>` blocks. No build step, no framework, no bundler, no external CSS/JS.
2. **No external fonts, scripts, images, or trackers — ever.** Type stack is exactly the
   current one: Georgia, serif for display; `system-ui, -apple-system, Segoe UI, Roboto,
   sans-serif` for body. New assets, if any, must live in this repo and be referenced
   relatively. This spec requires **no** new assets.
3. **Market Ledger and both themes preserved.** The CSS custom properties on `:root`
   (light) and `:root[data-theme="dark"]` (dark) ship with their current values,
   unchanged:

   | Token | Light | Dark |
   |---|---|---|
   | `--bg` | `#f6f3ec` | `#0d130f` |
   | `--surface` | `#fff` | `#16211a` |
   | `--surface2` | `#fdfbf7` | `#1a261e` |
   | `--ink` | `#1a211c` | `#ece7d9` |
   | `--muted` | `#5c665e` | `#9aa79d` |
   | `--line` | `#e3ddd0` | `#2b3a2e` |
   | `--green` | `#14532d` | `#35b267` |
   | `--green2` | `#166534` | `#7bc98f` |
   | `--lime` | `#dcfce7` | `#17351f` |
   | `--marigold` | `#d97706` | `#e8a33d` |

   All existing component classes that survive (`.pill`, `.badge`, `.card`, `.hero-deal`,
   `.price`, `.best`, `.tw`, `.add`) keep their current visual definitions, adjusted only
   where §6 density rules say so explicitly. New components must use these variables —
   no new hard-coded hex values anywhere except the two that already exist in the file
   (`#fff` text on `.badge` / green fills). The dark-theme badge override
   (`:root[data-theme="dark"] .badge{background:#1e7a46}`) is preserved verbatim.
4. **Theme toggle mechanism unchanged.** See §7.3. Same `localStorage` key (`hb-theme`),
   same early `<head>` script, same `:root[data-theme]` attribute, same button id
   (`themeToggle`), same label flip ("Dark mode" / "Light mode"). Only its placement
   changes (into the slim header, §5.1).
5. **My List storage contract unchanged.** See §7.4. Same key (`haulboard-list-v1`), same
   value format (JSON array of plain strings), same add semantics. Any list a user saved
   on the old page must appear, intact, on the redesigned page.
6. **Privacy (blocking).** The public page contains: no phone numbers, no owner's
   personal name, no home address. Only "Stafford, TX 77477" and public store addresses
   may appear. The pre-push privacy scan in DEVELOPMENT.md still runs and still blocks.
7. **Service worker bump is part of this build.** In `sw.js`, `CACHE` changes
   `'haulboard-v2'` → `'haulboard-v3'` and the header comment "shell v2" → "shell v3".
   Nothing else in `sw.js` changes (network-first for pages, cache-first for icon and
   manifest, same `SHELL` list). This bump is what makes installed phones drop the old
   single-scroll shell.
8. **`manifest.webmanifest` is unchanged** — byte for byte. `icon.svg` unchanged.
9. **File size:** `index.html` stays well under 1 MB (DEVELOPMENT.md rule). The redesign
   adds structure and a small view-switching script; it must not add libraries to do it.
10. **Copy rules:** plain language, sentence case, no emojis anywhere in UI copy. Existing
    copy is preserved verbatim unless §4 explicitly re-homes or splits it. Prices, dates,
    and qualifiers are never reworded, rounded, or "tidied" during the rebuild.

---

## 2. Decisions taken on ambiguous points (decision log)

The current page left these open. Each decision below is final for the builder; the
reasoning is recorded so a future run does not re-litigate it.

| # | Ambiguity | Decision | Why |
|---|---|---|---|
| D1 | The **watchlist** section has no natural tab (the tab bar is fixed at six views, §3) | Watchlist lives **inside the Stores view**, as its last group, titled "Store watchlist", preserving its heading and its "Checked every Friday." line. See §4 and §5.3 | The watchlist is a list of stores, not of deals. It belongs with the store index, and a seventh tab would crowd a 360px tab bar past usability |
| D2 | **Big retailers** (H-E-B, Walmart, Aldi, Kroger) is one card with one bundled bullet per retailer | It becomes a **group in the Stores index** titled "Big retailers", with **one detail view per retailer**, each retailer's bullet split into one deal row per priced item (§5.4) | Tapping a store opens its deals is the app pattern the owner asked for; the big four are stores. The group title and the section's intro line are preserved |
| D3 | Several current bullets **bundle multiple priced items** in one `<li>` (e.g. one Top picks bullet carries paratha + frozen veg + rice; one H-E-B bullet carries three items) | Bundled bullets are **split into one deal row per priced item**, every price and qualifier carried over verbatim. A bullet with no price (e.g. "Papa Mart — Diwali deals + meat flyer") becomes a row with no price and a "See store" affordance that opens that store's detail | Deal rows (§5.5) are one item each — that is what makes the price readable at arm's length. Splitting loses nothing; bundling is what made the old page unscannable |
| D4 | DEVELOPMENT.md says "keep the section ids stable" — but app views retire most section ids as scroll targets | **Hash compatibility replaces id stability.** All ten legacy hashes keep working via the parser in §7.5 and the mapping table in §3.3. A small set of ids is retained as DOM ids for in-view scrolling and JS hooks (listed in §3.3, note b). After this build ships, DEVELOPMENT.md's validation item 2 should be updated by the docs owner to point at §3.3 of this spec — the builder of *this* spec does not edit DEVELOPMENT.md | Keeping every old id as a live element would force the single-scroll structure to survive inside the app shell. The user-facing contract is the *link*, not the element |
| D5 | Only the headline deal currently has an "Add to my list" button | Add buttons are **extended to every priced deal row** in This Week and store detail views (and coupon shortlist rows, §5.6), using the string format in §7.4 | The task for this redesign names add buttons in deal rows across views; a list you can only add one item to is the Phase-0 stub the current page itself apologises for |
| D6 | My List currently supports add and clear-all only — **no per-item remove** | Per-item remove is **added**, as a purely additive change on top of the unchanged storage contract (§7.4) | The redesign brief requires remove; it can be built without changing the key, the format, or clear behaviour |
| D7 | The **footer** (repo / documentation / roadmap links) has no view | The footer block lives at the **bottom of the This Week view only**. The trust line "Prices were verified for the week shown; check the flyer before you go." is additionally repeated as small print at the bottom of the Stores and Compare views | A footer under a fixed tab bar on every view is dead weight; This Week is the home screen and the natural place for it. The trust line is load-bearing on the two other data views |
| D8 | Three watchlist stores (**Zaina Supermarket, Prime Bazaar, The Meat Grinder Market**) have no section or prices on the current page | They appear as **rows in the watchlist group**, and — like every store row — they are tappable and open a **minimal detail view**: store name, the watchlist note that exists for them on the current page (Meat Grinder's address and "in-store prices only" note; the generic status template in §5.4 for the other two), and no deal rows | "Tapping a store opens its detail" must hold for every row, or the index teaches the user that some rows are broken. No prices, addresses, or facts are invented for these stores beyond what the current page and OPERATIONS.md already state |
| D9 | A **festival pantry** section (Diwali / Ramadan / Eid non-produce, non-meat deals) exists in OPERATIONS.md but is not a separate section on the current page (festival deals are inline in store sections) | This Week carries an **optional slot** for it (§5.2, marker in §8). When no festival sale is live, the slot is omitted entirely — no heading, no empty card | Gives the Friday run a home for festival content without inventing a seventh view or showing an empty section 50 weeks a year |
| D10 | RUNBOOK.md says dashboard items link their source flyer post; the current GitHub Pages page has **no** such links | Store detail anatomy includes a **flyer-link slot** (§5.4, item 6), rendered only when the week's data carries a source permalink for that store. Absent data → slot absent, no placeholder | Specifying the slot now costs the builder one conditional; retrofitting it later would break the marker layout in §8 |

---

## 3. View map

### 3.1 The six views + one sub-view

| Tab label | View id | Canonical hash | Job in one line |
|---|---|---|---|
| This Week | `this-week` | `#this-week` | The answer screen: headline deal + top picks |
| Stores | `stores` | `#stores` | Compact index of every store; opens store detail |
| Compare | `compare` | `#price-compare` | Best price per item, all category tables |
| Coupons | `coupons` | `#coupons` | Weekly coupon shortlist + standing coupon notes |
| Free Food | `free-food` | `#free-food` | Free food distributions near Stafford |
| My List | `my-list` | `#my-list` | The user's own list, on-device only |

Plus one **sub-view that is not a tab**: **Store detail** (`store-detail`), opened from
the Stores index, belonging to the Stores tab (the Stores tab stays selected while a
detail is open). §5.4.

Rules that hold for the whole map:

- **Exactly one view is visible at a time** — the six views and the store-detail screen
  are mutually exclusive. Hidden views use the `hidden` attribute (with
  `.view[hidden]{display:none}` in CSS as belt and braces).
- **This Week is the default view** — shown on load when the hash is empty, `#`, an
  unrecognised value, or `#this-week`.
- Each view is a **screen, not a page segment**: it starts at the top under the shared
  header (§5.1), has its own title, and scrolls independently in the normal document
  flow (no inner scroll containers except the Compare tables, §5.5).
- Views are `<section class="view" id="view-<slug>" role="tabpanel">` elements inside one
  `<main>`. The store detail is `<section class="view" id="view-store-detail">` whose
  content is one of the per-store blocks in §8 (only the opened store's block is shown;
  the others stay `hidden` inside it — the builder may instead use one detail shell with
  fifteen child `<div class="store-block" data-store="<slug>" hidden>` blocks; the
  markers in §8 are written for the child-block form, which the Friday run can regenerate
  block by block).

### 3.2 Navigation chrome

- **Mobile (≤760px — the existing breakpoint, kept): a fixed bottom tab bar.** One
  `<nav id="tabbar" aria-label="HaulBoard views">` containing six `<button type="button"
  role="tab">` elements in the order of the table above. Fixed to the bottom,
  `background:var(--surface)`, top border `1px solid var(--line)`, height **60px plus
  `env(safe-area-inset-bottom)`** of bottom padding. Each tab is flex:1, minimum height
  56px, label only (no icon font, no external icons; an inline SVG glyph per tab is
  permitted but not required, and if used must be stroke `currentColor` so both themes
  work). Active tab: label and glyph in `var(--green)` (dark theme: `var(--green2)`),
  weight 800, with a 3px `var(--marigold)` indicator bar across the top of the tab.
  Inactive tabs: `var(--muted)`, weight 700.
- **My List tab carries a count badge**: a small pill (marigold background, `#fff` text,
  11px, min-width 20px) showing the number of saved items, rendered only when the count
  is > 0. It is updated by the same `render()` that renders the list (§7.4).
- **Desktop (≥761px): the same `<nav>` becomes a top bar.** Via media query only — one
  nav element, no duplication: `position:sticky; top:56px` (directly under the sticky
  header), `bottom:auto`, full width of the wrap, tabs rendered as the current page's
  pill nav (surface background, 1px `--line` border, 999px radius, ink text; active pill
  filled `var(--green)` with `#fff` text). The bottom-bar styles are fully overridden in
  this breakpoint; nothing of the bottom bar may leak into desktop.
- **Content max-width stays ~1100px**: `.wrap{max-width:1100px;margin:0 auto}` as now,
  padding `0 18px` on desktop, `0 16px` on mobile (§6).
- **Page bottom padding on mobile:** `padding-bottom: calc(60px +
  env(safe-area-inset-bottom) + 24px)` on `.wrap` (or an equivalent spacer) so the fixed
  tab bar never covers content — this replaces the current flat `48px`.

### 3.3 Deep-link mapping (legacy hashes → views)

Every hash that works on the current page must keep working. On load and on every
`hashchange`, the router (§7.5) parses `location.hash` against this table:

| Incoming hash | Opens | Then |
|---|---|---|
| *(empty)*, `#`, `#this-week` | This Week | Scroll to top |
| `#top-picks` | This Week | Scroll to the Top picks card |
| `#al-aqsa` | Stores → **Al Aqsa Bangla Bazaar detail** | — |
| `#wfw` | Stores → **World Food Warehouse detail** | — |
| `#more-stores` | Stores | Scroll to the "More stores" group heading |
| `#big-retailers` | Stores | Scroll to the "Big retailers" group heading |
| `#watchlist` | Stores | Scroll to the "Store watchlist" group heading |
| `#price-compare` | Compare | Scroll to top |
| `#compare` *(new alias)* | Compare | Scroll to top |
| `#coupons` | Coupons | Scroll to top |
| `#free-food` | Free Food | Scroll to top |
| `#my-list` | My List | Scroll to top |
| `#stores` *(new)* | Stores index | Restore saved index scroll (§7.2) |
| `#<store-slug>` for every other store (§3.4) | Stores → that store's detail | — |
| Any other value | This Week | Scroll to top; never an error, never a blank screen |

Notes:

a) **Scrolling to a target happens after the view is unhidden** — in the same frame the
   router removes `hidden` from the target view, then calls
   `target.scrollIntoView({block:'start'})` adjusted for the sticky header (use
   `scroll-margin-top: 72px` on scroll-target elements; desktop: `128px` to clear header
   + top tab bar). A native browser jump must never be relied on: elements inside a
   `hidden` view cannot be scrolled to, which is exactly why the router, not the browser,
   owns all hash behaviour.
b) **Retained DOM ids** (each exactly once, used as scroll targets and JS hooks):
   `top-picks` (the Top picks card), `more-stores`, `big-retailers`, `watchlist` (the
   three group wrappers in Stores), `price-compare`, `coupons`, `free-food`, `my-list`
   (the four view sections themselves), plus the functional ids `themeToggle`, `list`,
   `clear` (§7.3–7.4). The legacy store section ids `al-aqsa` and `wfw` are **not**
   retained as element ids — they are handled purely as hash routes to store slugs,
   because the detail blocks are keyed by `data-store` (§8). This is decision D4.
c) Store rows in the index are real anchors, `<a href="#<slug>">`, so a link can be
   long-pressed, copied, and shared; the router intercepts them through `hashchange`
   (§7.5). Tab buttons are `<button>`s, not anchors — tabs are view switches, not links.

### 3.4 Store slug registry

Slugs are the hash routes, the `data-store` keys, and the §8 marker names. Fixed set:

| Store (display name) | Slug | Current-page origin |
|---|---|---|
| Al Aqsa Bangla Bazaar | `al-aqsa` | `#al-aqsa` section |
| World Food Warehouse | `wfw` | `#wfw` section |
| Papa Mart | `papa-mart` | More stores |
| Desi Brothers (Sugar Land) | `desi-brothers` | More stores |
| Big Basket (Stafford) | `big-basket` | More stores |
| Al Madina Supermarket | `al-madina` | More stores |
| Jerusalem Supermarket | `jerusalem` | More stores |
| Hala Market (Sugar Land) | `hala-market` | More stores |
| H-E-B | `heb` | Big retailers |
| Walmart | `walmart` | Big retailers |
| Aldi | `aldi` | Big retailers |
| Kroger | `kroger` | Big retailers |
| Zaina Supermarket | `zaina` | Watchlist only (D8) |
| Prime Bazaar | `prime-bazaar` | Watchlist only (D8) |
| The Meat Grinder Market | `meat-grinder` | Watchlist only (D8) |

Short labels used inside `data-item` strings (§7.4): Al Aqsa, World Food Warehouse,
Papa Mart, Desi Brothers, Big Basket, Al Madina, Jerusalem, Hala Market, H-E-B, Walmart,
Aldi, Kroger, Zaina, Prime Bazaar, Meat Grinder — i.e. the display name minus any
parenthetical, with "Al Aqsa" and "Meat Grinder" shortened as in the existing headline
deal string.

---

## 4. Content mapping — nothing is lost

Every section of the current page, and where each piece lands. The builder works down
this table; the critique agent audits against it. "Split" refers to decision D3.

| # | Current content (in page order) | New home | Treatment |
|---|---|---|---|
| 1 | Sticky pill nav (10 anchor links) | **Removed as a component** | Replaced by the tab bar + store anchors (§3.2). Its *targets* all survive via §3.3 |
| 2 | Header eyebrow: "Stafford, TX 77477 · Weekly · As of 2026-10-24" | Slim header (date part) + This Week lede block (location part) | Date becomes the header week stamp (§5.1); the full eyebrow line is preserved at the top of This Week (§5.2) |
| 3 | H1 "HaulBoard — Know before you go." + lede paragraph | This Week lede block | Title restyled to §6 sizes; wording verbatim |
| 4 | Three pills: prices filed count · "Halal meat tracked" · "Live board — updated every Friday" | This Week lede block, unchanged | Static pills (not interactive), §6 pill spec |
| 5 | Hero deal card (headline deal, VERIFIED badge, Add button) | This Week, first content block | Keeps full hero treatment (§5.2). Its Add button and `data-item` string are preserved character-for-character |
| 6 | `#top-picks` — 5 bullets | This Week → Top picks card | Bullets split into deal rows (D3). The no-price Papa Mart bullet becomes a "See store" row opening `papa-mart` detail |
| 7 | `#al-aqsa` — store card (address line + 5 bullets) | Stores → `al-aqsa` detail | Address/notes line becomes the detail meta line; bullets become deal rows |
| 8 | `#wfw` — store card (address line + 5 bullets) | Stores → `wfw` detail | Same as row 7 |
| 9 | `#more-stores` — Papa Mart block | Stores → `papa-mart` detail | Meta line preserved ("Open 24/7 · Free delivery over $25" included) |
| 10 | `#more-stores` — Desi Brothers block | Stores → `desi-brothers` detail | Bullets split into rows (beet root / onion / carrots are three items at three prices in one bullet — three rows) |
| 11 | `#more-stores` — Big Basket block | Stores → `big-basket` detail | Same split rule |
| 12 | `#more-stores` — Al Madina block (no prices; "No new flyer priced this week…" text) | Stores → `al-madina` detail | Status text preserved verbatim as the detail's status note (§5.4 item 4); zero deal rows |
| 13 | `#more-stores` — Jerusalem block (in-store-only text) | Stores → `jerusalem` detail | Status text preserved verbatim; zero deal rows |
| 14 | `#more-stores` — Hala Market block | Stores → `hala-market` detail | Two bullets → two rows (the four $5.99/lb cuts stay as one row, exactly as listed — one price, one line) |
| 15 | `#big-retailers` — intro line ("Weekly ads Wed–Tue. Spot-check…") | Stores → Big retailers group intro **and** each of the four retailer details | Group intro verbatim; in each retailer detail it is the intro line under the meta |
| 16 | `#big-retailers` — H-E-B bullet (3 items) | Stores → `heb` detail | Split into 3 deal rows |
| 17 | `#big-retailers` — Walmart bullet (2 items) | Stores → `walmart` detail | Split into 2 deal rows |
| 18 | `#big-retailers` — Aldi bullet (4 items + "Aldi has no coupon program.") | Stores → `aldi` detail | Split into 4 deal rows; the no-coupon-program sentence becomes the detail's status note |
| 19 | `#big-retailers` — Kroger bullet (4 items) | Stores → `kroger` detail | Split into 4 deal rows |
| 20 | `#price-compare` — intro line with price count and date | Compare → view intro | Verbatim, inside the §8 data markers (count and date change weekly) |
| 21 | `#price-compare` — master table (Item / Category / Best / All stores, 21 rows) | Compare → first table | Table preserved as a table (§5.5); column set unchanged |
| 22 | `#price-compare` — 8 category tables (Meat & poultry, Produce, Other groceries, Dairy/frozen & breads, Grains/rice & flour, Oils & ghee, Spices & masalas, Dals & beans), each Item / Best price / Store | Compare, same order, same `<h3>` headings | Tables preserved as tables; no rows merged, dropped, or re-sorted by the builder |
| 23 | `#price-compare` — sample basket line ("Sample basket split total… $95.71…") | Compare → closing card under the tables | Verbatim |
| 24 | `#coupons` — 5 bullets (H-E-B, Kroger, Walmart, Aldi, Papa Mart) | Coupons → "Standing notes" block | One note row per bullet, wording verbatim, **below** the weekly shortlist slot (§5.6). The Aldi bullet appears here *and* as the Aldi detail status note (row 18) — that duplication exists on the current page's logic and is kept deliberately |
| 25 | `#free-food` — intro line ("Re-verify each run… No phone numbers…") | Free Food → view intro card | Verbatim |
| 26 | `#free-food` — 3 organisation bullets | Free Food → organisation rows | Org name + area + schedule as listed; no data added |
| 27 | `#watchlist` — heading + "Checked every Friday." + 4 bullets | Stores → "Store watchlist" group (D1) | Bullet 1 (5 stores) and bullet 2 (5 stores) are already index rows elsewhere in Stores — the group itself lists the three watchlist-only stores (Zaina, Prime Bazaar, Meat Grinder) as rows, keeps the heading and the "Checked every Friday." line verbatim, keeps Meat Grinder's full bullet text (address + notes) in its detail **and** as its row sub-line, and keeps bullet 4 (H-E-B · Walmart · Aldi · Kroger · Flipp) as a text line at the foot of the group, with Flipp identified as an ad source, not a store. Every name in the original 4 bullets is accounted for by a row or that line |
| 28 | `#my-list` — heading, on-device note, `<ul id="list">`, Clear button | My List view | §5.7 and §7.4. The note is preserved, shortened only by dropping the roadmap clause ("…full trip planner is Phase 1") — a product-status aside, not user data; the sentence "Saved only on this device (local storage)." is kept verbatim |
| 29 | Footer (Friday note + Repo / Documentation / Roadmap links) | Bottom of This Week (D7) | Verbatim, links unchanged |

**Audit rule for the builder:** after the rebuild, a text diff of visible copy between
old and new page may show only: split bullets rendered as separate rows, the D7/D28
trims named above, group headings in Stores, tab labels, and the new UI strings defined
in this spec ("See store", "Back to stores", view titles, "Nothing added yet." is
existing). Any other missing sentence is a defect.

---

## 5. Per-view layout

### 5.1 Shared chrome: slim sticky header (all views)

One `<header>` above `<main>`, `position:sticky; top:0`, height **56px**,
`background:var(--bg)`, bottom border `1px solid var(--line)`, `z-index:6` (above the
desktop tab bar's 5). Contents, left to right, inside the wrap width:

1. **Wordmark:** "HaulBoard" — Georgia, 700, 20px, `var(--ink)`, with "Know before you
   go." in `var(--green2)` on desktop (≥761px) and omitted on mobile (space). The
   wordmark is a `<p class="brand">`, not a heading — each view carries its own title
   (§5.2–5.7), so the page never has six competing `<h1>`s. Exactly one `<h1>` exists in
   the DOM: the This Week title (§5.2); other view titles are `<h2>`.
2. **Week stamp:** the run date, format "As of Oct 24" (full date in the This Week
   eyebrow), 12.5px, `var(--muted)`, immediately right of the wordmark on mobile,
   pushed right on desktop. Sourced from the §8 `week-stamp` data region — it is weekly
   data, not shell text.
3. **Theme toggle:** the existing `#themeToggle` button, moved here, last item, right
   aligned. Restyled only to meet §6: `min-height:44px`, padding `0 14px`, otherwise the
   current `.theme-toggle` look (surface background, line border, pill radius, 12.5px
   weight 700). The current `float:right` is removed (flex header now).

The tab bar (§3.2) is the only other shared chrome. There is no second sticky element
on mobile; on desktop the header (56px) and tab bar beneath it form one sticky stack
(total 56px + tab bar height; scroll-margin values in §3.3 already account for it).

### 5.2 This Week (`#this-week`)

Order top to bottom:

1. **Lede block** (not a card — sits on the paper background, as the current header
   does): the eyebrow line verbatim ("Stafford, TX 77477 · Weekly · As of
   2026-10-24" — date inside §8 markers); `<h1>` "HaulBoard — Know before you go."
   restyled to 30px mobile / 44px desktop Georgia (down from the current
   `clamp(36px,6vw,60px)` — the hero moment now belongs to the deal, not the title);
   the lede paragraph verbatim; the three pills verbatim (§4 row 4).
2. **Headline deal** — keeps the full current hero treatment, the one tall card in the
   app: `.hero-deal` exactly as today (`--lime` background, 1.5px `--green2` border,
   6px `--marigold` left border, 14px radius, padding 16px mobile / 18px desktop).
   Inside: `<h2>` "Headline deal" (22px Georgia) + `VERIFIED` badge; the `.big` price
   line (24px mobile / 26px desktop, weight 800, `--green`) verbatim; the detail line
   (store · halal note · limit · source) at 14.5px; the Add button — full-width on
   mobile, auto-width on desktop, `min-height:48px`, filled variant (background
   `var(--green)`, text `#fff`, pill radius, 14px weight 700), `data-item` string
   preserved character-for-character. This button is the one `.add-primary`; all other
   add buttons use the standard `.add` (§5.5).
3. **Top picks card** (`id="top-picks"`, `.card`): `<h2>` "Top picks", then deal rows
   (§5.8) — one per priced item after the D3 split (current content yields 7 priced rows
   + the Papa Mart "See store" row).
4. **Festival pantry slot** (D9): a `.card` with `<h2>` from the week's data (e.g.
   "Diwali pantry"), deal rows inside. **Omitted entirely when no festival data exists.**
5. **Footer block** (D7): the current footer text and its three links, 13px
   `var(--muted)`.

### 5.3 Stores — index (`#stores`)

1. View title `<h2 class="view-title">` "Stores" + one-line intro: "Every store checked
   on the Friday run. Tap a store for this week's deals." (new UI string; the only
   invented sentence in this view besides row sub-lines generated from data).
2. **Featured rows** — Al Aqsa and World Food Warehouse first, ungrouped, in current
   page order. (They were full sections; every other store was a sub-block. The index
   preserves that hierarchy without a heading.)
3. Group wrapper `id="more-stores"`: heading `<h3>` "More stores", store rows for
   Papa Mart, Desi Brothers, Big Basket, Al Madina, Jerusalem, Hala Market (page order).
4. Group wrapper `id="big-retailers"`: heading `<h3>` "Big retailers", the section intro
   line verbatim as group sub-line, rows for H-E-B, Walmart, Aldi, Kroger.
5. Group wrapper `id="watchlist"`: heading `<h3>` "Store watchlist", sub-line "Checked
   every Friday." verbatim, rows for Zaina, Prime Bazaar, The Meat Grinder Market, then
   the closing text line from §4 row 27.
6. Trust small print (D7): "Prices were verified for the week shown; check the flyer
   before you go." 12.5px `var(--muted)`.

**Store row anatomy** (an `<a class="store-row" href="#<slug>">`, full-row tap target,
min-height 64px, padding 12px, bottom divider `1px solid var(--line)`, rows grouped
inside one `.card` per group — dividers, not fifteen separate cards):

- Line 1: store display name, 16px weight 600 — right side: chevron "›" (20px,
  `var(--muted)`) signalling the row opens a detail.
- Line 2 (13px, `var(--muted)`): locality + status, from data — e.g. "Houston · 5 deals
  this week", "Stafford · 2 deals this week", or exactly one of the status phrases:
  "No flyer priced this week" (Al Madina), "In-store only" (Jerusalem), "Watchlist only"
  (the three D8 stores).
- Line 3, only when the store has priced deals (13.5px, weight 700, `var(--green)`):
  best-deal teaser, format "Best: <item> — <price>" using that store's lowest or
  headline item as listed first in its current section (e.g. Al Aqsa: "Best: Goat Mix
  Tray — $5.99/lb weekend"). The teaser is data, regenerated weekly (§8), never
  invented copy.

**Desktop (≥761px):** each group's rows render in the existing two-column `.grid`
(1fr 1fr, gap 14px); the card-per-group structure is unchanged. Mobile is always one
column.

### 5.4 Stores — store detail (sub-view)

Opened by a store row or a store deep link. Replaces the index; Stores tab stays
selected. Anatomy, top to bottom:

1. **Back control:** `<button class="back-btn">` "‹ Back to stores" — min-height 44px,
   text `var(--green2)` weight 700, no border/background. Behaviour in §7.2.
2. **Store name:** `<h2>` 24px Georgia (28px desktop).
3. **Meta line** (13.5px, `var(--muted)`): the store's current address + notes line,
   verbatim from the current page (e.g. "6198 Wilcrest Dr, Houston, TX 77072 · Zabiha
   halal · Flyers usually Thu/Fri"). For big retailers: address line is the retailer's
   local descriptor as currently implied ("Stafford / Sugar Land" for H-E-B, "Sugar
   Land, Hwy 6" for Walmart; Aldi and Kroger have no locality on the current page — their
   meta line is the ad-window line from §4 row 15 instead; do not invent addresses for
   them). For watchlist-only stores: the meta line is their watchlist note where one
   exists (Meat Grinder: "8002 FM 1464 Rd, Richmond, TX 77407 · Halal, premium"),
   otherwise the area descriptor from the runbook is *not* to be pulled in — use
   "On the Friday watchlist" (see status note).
4. **Status note** (only when the store has no priced deals this week): a
   `.card`-inset block (`--surface2` background) carrying the store's existing text
   verbatim — Al Madina's "Weekday Deals Mon–Wed flyer most weeks. No new flyer priced
   this week — check the latest flyer on the Friday run.", Jerusalem's "In-store only
   for now; no posted prices found in Oct 2026. Kept on the watchlist.", Aldi's "Aldi
   has no coupon program." — or, for the two D8 stores with no existing text, exactly:
   "On the Friday watchlist. No priced deals on this week's board."
5. **Deal rows** (§5.8): one per priced item, in the order items appear in the store's
   current section. Store name is omitted from each row's meta (the screen already says
   which store this is); the row meta carries the item's qualifiers (days, limits,
   "bulk only", etc.) exactly as listed.
6. **Flyer-link slot** (D10): if the week's data includes a source permalink for this
   store, one link under the rows: "View flyer post" (`var(--green2)`, weight 600,
   min-height 44px). If no permalink: nothing renders.
7. Big-retailer details only: the §4 row 15 intro line appears as small print under the
   rows if it was not already used as the meta line.

### 5.5 Compare (`#price-compare`)

1. View title "Price compare" + the intro line verbatim (§4 row 20) at 14.5px
   `var(--muted)`.
2. **Master table**, then the **eight category tables** in current order, each preceded
   by its current `<h3>` heading verbatim. Tables stay tables — this data is a lookup
   grid, and deal rows would destroy the cross-store scan.
3. Each table sits in the existing `.tw` wrapper (`overflow-x:auto`). Table rules:
   master table `min-width:620px`, category tables `min-width:420px`; the wrapper is
   `tabindex="0" role="region" aria-label="<heading> table — scrolls sideways"` so
   keyboard and screen-reader users can pan it; a right-edge fade (linear-gradient mask
   using `--bg`) hints scrollability on mobile. The **page itself never scrolls
   horizontally** — only the `.tw` regions do.
4. `.best` cells keep their current styling (weight 700, `var(--green)`). Header cells:
   12px, weight 800, uppercase, letter-spacing .06em. Body cells 14px, padding 8px 10px,
   row min-height 44px — all as §6.
5. **No Add buttons in Compare** (decided): the table stays narrow and scannable; the
   same items are addable from This Week and store details. The Best cell's store name
   is plain text, not a link — keeping 100+ table cells link-free is what keeps this
   view fast to pan.
6. Closing sample-basket card (§4 row 23), then the trust small print (D7).

### 5.6 Coupons (`#coupons`)

1. View title "Coupons" + intro line (new UI string): "The week's shortlist first, then
   the standing notes for each store."
2. **Weekly shortlist slot** (§8 marker `coupons-shortlist`): when the Friday run files
   a shortlist (OPERATIONS.md step 2 format), it renders here as: an optional "Top 5"
   group of **coupon rows**, then per-store groups of coupon rows. **Coupon row
   anatomy:** line 1 — item + size (16px weight 600) and final price right (20px,
   weight 800, `var(--green)`); line 2 (13px `var(--muted)`) — store · shelf price ·
   where to clip · validity dates; a `VERIFIED` badge or an `UNVERIFIED` badge
   (variant class `.badge-warn`, background `var(--marigold)`, text `#fff`) where the
   data carries that flag; an Add button (§5.8) when the row is a purchasable grocery
   item with a final price. The posters' deal math is reproduced **as posted, never
   recomputed** (standing rule from OPERATIONS.md). When no shortlist has been filed
   yet, the slot renders one muted line: "This week's shortlist lands with the Friday
   run." — and nothing else; the standing notes below are the view's standing content.
3. **Standing notes block:** heading `<h3>` "Standing notes", then the five current
   bullets (§4 row 24) as note rows — store name in weight 700 lead-in, text verbatim,
   15px, separated by `--line` dividers inside one `.card`. No Add buttons on note rows.

### 5.7 Free Food (`#free-food`)

1. View title "Free food" + the intro card carrying the current intro text verbatim
   (§4 row 25), including the no-phone-numbers sentence.
2. **Organisation rows** inside one `.card`, divider-separated, min-height 56px: org
   name 16px weight 600; second line 14px `var(--muted)` — area and schedule exactly as
   currently listed (e.g. "Stafford — Tue / Wed evening / Thu / Fri distributions",
   "Wed Oct 28", "Sat Oct 31"). Dates are weekly data (§8 markers) — they change every
   run and must never be hard-coded into the shell. No Add buttons, no phone numbers,
   no external links in this view.

### 5.8 My List (`#my-list`) and the deal-row component

**Deal row** (`.deal-row`) — the workhorse component, used in Top picks, store details,
the festival slot, and (as coupon rows, §5.6) Coupons. Anatomy:

- Container: block, padding 10px 0 (12px horizontal inset when inside a card — the card
  provides it), bottom divider `1px solid var(--line)`; last row in a card has no
  divider. Min-height 60px. Rows are **not** nested cards.
- Layout (flex, two columns): left block flex:1 — item name (16px, weight 600,
  line-height 1.35), under it the meta line (13px, `var(--muted)`, line-height 1.45):
  store short label (omitted in store detail), qualifiers/dates, and a `VERIFIED`
  badge inline where the current page badges that item. Right block — price (18px,
  weight 800, `var(--green)`, right-aligned, `white-space:nowrap`; the unit suffix
  `/lb`, `each`, `dozen` renders at 13px weight 600) with the **Add button directly
  under it**, right-aligned.
- **Add button** (`.add`, restyled to §6): `min-height:44px; min-width:72px;
  padding:0 14px; font-size:13px; weight 700; border:1px solid var(--line);
  background:var(--surface2); border-radius:999px`. Label is "Add" (the current label
  "Add to my list" is retained **only** on the headline deal's primary button — the row
  button sits under a price on a board whose only list is My List; the tab badge and
  §7.4 feedback make the destination unambiguous, and a 44px "Add to my list" in every
  row is what made rows tall). `aria-label` on every row Add button carries the full
  string: `aria-label="Add <item> to my list"`.
- The current page's `<ul>/<li>` deal lists are replaced by these rows; list semantics
  are kept by wrapping rows in `<ul class="rows">` with `<li class="deal-row">` where
  practical, or `role="list"`/`role="listitem"` — the builder picks one and is
  consistent.

**My List view:**

1. View title "My list" + the preserved note "Saved only on this device (local
   storage)." at 14.5px `var(--muted)`, plus a live count line: "<n> items" (13px
   `var(--muted)`), updated by `render()`; hidden when the list is empty.
2. `<ul id="list">` — **the id and element are contractual** (§7.4). Items render as
   rows: item string at 16px (the strings are self-describing — "Item — Store —
   Price"), and a per-item **Remove button** at the row's right: 44×44px, label "✕" is
   **not** used (character/entity risk and clarity) — the button text is "Remove" at
   13px weight 700, `var(--muted)`, with `aria-label="Remove <item>"`.
3. **Clear button** (`id="clear"` — contractual): below the list, `min-height:44px`,
   padding `0 16px`, current `.add` styling, label "Clear list" verbatim. Behaviour
   unchanged (§7.4) — one tap, no confirmation dialog (the builder must not invent
   one).
4. Empty state: the existing string, verbatim — "Nothing added yet." — rendered as the
   single `<li class="muted">` exactly as the current `render()` does.
5. Small print: "Use Add on any deal to put it here before the trip." (new UI string,
   the only invented line in this view.)

---

## 6. Density rules (mobile-first, concrete numbers)

The owner's complaint is density and reach, so these are numbers, not vibes. Anything
not listed here keeps its current value.

### 6.1 Type scale

| Role | Size / weight / line-height | Notes |
|---|---|---|
| Brand wordmark (header) | 20px Georgia 700 / 1.1 | §5.1 |
| Page H1 (This Week only) | 30px Georgia 700 / 1.05 mobile; 44px desktop | Down from clamp(36–60px) |
| View titles, store names (h2) | 24px Georgia 700 / 1.15; 28px desktop | Matches current `h2` at desktop |
| Card / group headings (h2 in cards, h3 groups) | 20px Georgia 700 (card h2) · 17px sans 700 (group h3) / 1.3 | Group h3 down from browser-default ~18.7px |
| Headline price line (`.big`) | 24px 800 / 1.25; 26px desktop | Current 26px kept at desktop |
| Deal item name | 16px 600 / 1.35 | Minimum for any product name |
| Deal price | 18px 800 / 1.2, unit 13px 600 | "Readable at arm's length" = the price test in §9 |
| Coupon final price | 20px 800 / 1.2 | §5.6 |
| Body / intros / note text | 15px 400 / 1.55 (14.5px for detail lines) | No body text under 14px anywhere |
| Meta lines (store, deal, org) | 13px 400 / 1.45, `var(--muted)`; store meta 13.5px | Meta is the only sub-14px text class besides pills/badges |
| Table body | 14px 400 / 1.45 | Current 14px kept |
| Table headers | 12px 800 uppercase, ls .06em | |
| Pills (static) | 12.5px 700, padding 6px 10px, min-height 30px | Current `.pill`, padding tightened |
| Badges | 10.5px 800, ls .08em, padding 3px 7px | Current `.badge` verbatim; badges are never interactive |
| Tab labels | 11px 700; active 800 | 11px is allowed here only because tabs also carry position + indicator |
| Buttons (Add, Clear, Back, theme) | 13–14px 700 | See tap targets |
| Footer / small print | 12.5–13px, `var(--muted)` | |

### 6.2 Spacing scale and layout numbers

- **Scale: 4 / 8 / 12 / 16 / 24 / 32.** No off-scale spacing values in new CSS.
- Page gutter: 16px mobile, 18px desktop (current `.wrap` padding kept at desktop).
- View top padding: 16px. Gap between blocks inside a view: 12px (cards keep
  `margin-top:14px`-equivalent rhythm — set card margin to 12px in the new system and
  use it consistently). Group gap inside Stores: 24px.
- Card padding: 14px mobile, 18px desktop (current 18px kept at desktop only).
- Card radius 18px, hero radius 14px, buttons/pills/badges 999px — all current values.
- Deal row: min-height 60px, vertical padding 10px, price column min-width 86px so
  prices align down a list.
- Store row: min-height 64px, padding 12px.
- Table: cell padding 8px 10px, row min-height 44px.

### 6.3 Tap targets (hard minimums)

- **Every interactive element is at least 44×44px** — tab buttons (56px tall in the
  bar), Add (44px tall, 72px wide), Remove (44×44), Clear (44px tall), Back (44px
  tall), theme toggle (44px tall), store rows (64px), flyer link and footer links get
  `min-height:44px` inline-flex hit areas where they are the primary action of their
  block.
- Adjacent targets are separated by at least 8px on all sides (tab bar excepted: tabs
  are full-height, full-width segments — separation is the segment boundary itself).
- Static pills and badges are exempt (they are not targets) and must not be styled to
  look tappable — no border darkening, no hover state.

### 6.4 Motion

- View switches are instant (show/hide, §7.1). No slide/fade transitions between views —
  on a mid-range phone, instant reads as faster and never janks.
- The only permitted transitions are current-state ones (button press feedback via
  `:active` background change). `@media (prefers-reduced-motion: reduce)` disables all
  transitions.

---

## 7. Interaction specification

### 7.1 Tab switching

- Implementation: vanilla JS `showView(viewId, opts)` — removes `hidden` from the
  target view (and, for store detail, from the target store block), sets `hidden` on
  every other `.view`, updates the tab buttons, updates the hash (§7.5), scrolls (§7.2).
  No framework, no routing library; the whole router + list + theme scripts must stay in
  the same order-of-magnitude as the current three inline scripts.
- Tab semantics (WAI-APG tabs pattern, automatic activation): the nav is
  `role="tablist"`; each button is `role="tab"` with `aria-selected="true|false"`,
  `aria-controls="view-<slug>"`, and a roving `tabindex` (selected tab `0`, others
  `-1`); each view is `role="tabpanel"` with `aria-labelledby` pointing at its tab.
  Left/Right arrow keys move between tabs on desktop keyboards; click/tap activates
  immediately. Enter/Space need no handler (native button + automatic activation).
- Active tab styling per §3.2. Switching to the already-active tab is a no-op (no hash
  churn, no scroll reset — except Stores, where tapping the active Stores tab while a
  detail is open returns to the index: that is the second way back, alongside §7.2's
  Back button).

### 7.2 Focus and scroll behaviour

- **Tab activated by tap/click:** focus stays on the tab button. Do not move focus into
  the panel — focus theft on every tap is the failure mode this rule exists to prevent.
- **View opened by deep link, `hashchange`, or a store row:** after the view is shown,
  focus moves to the view's title (store name in detail), which carries
  `tabindex="-1"`. Screen-reader and keyboard users land on the content they asked for.
- **Back to stores:** returns focus to the store row (`<a href="#<slug>">`) that opened
  the detail, and restores the index's saved scroll position. The router keeps one
  variable (`storesScrollY`) captured when a detail opens.
- **All other view switches scroll to top** (`window.scrollTo(0,0)` after unhiding),
  except the scroll-target cases in §3.3 (top-picks, group headings), which scroll to
  their target instead.
- Scroll positions of non-Stores views are not saved or restored. One saved position
  (Stores) is the whole contract — cheap and sufficient.

### 7.3 Theme toggle (mechanism unchanged — port, do not redesign)

Current implementation, which must survive byte-equivalent in behaviour:

1. An early inline script in `<head>`, before CSS paints:
   `try{var t=localStorage.getItem('hb-theme');if(t){document.documentElement.dataset.theme=t;}}catch(e){}`
2. CSS: `:root` carries the light variables; `:root[data-theme="dark"]` overrides them.
3. A button `#themeToggle` whose label is set from state — "Light mode" when
   `document.documentElement.dataset.theme==='dark'`, else "Dark mode" — and whose click
   handler flips `dataset.theme` between `'dark'` and `'light'`, persists with
   `localStorage.setItem('hb-theme', d)` inside `try/catch`, and re-labels.

Only changes permitted: the button's DOM location (header, §5.1), removal of
`float:right`, and padding/min-height to meet §6.3. The storage key, the attribute, the
labels, and the try/catch behaviour do not change. A user who chose dark on the old page
opens the new page in dark.

### 7.4 "Add to my list" — existing contract, ported unchanged, then extended

**The existing implementation's contract** (from the current page's script — this is
the specification of what must not change):

- Storage key: **`haulboard-list-v1`**. Value: a **JSON array of strings** in
  `localStorage`. Absent key = empty list. All reads/writes are wrapped in `try/catch`;
  a corrupt or unparseable value is treated as an empty list, never an exception.
- **Add:** a button with class `add` and a `data-item` attribute. On click, the handler
  parses the current array, **`push`es the `data-item` string verbatim**, saves with
  `localStorage.setItem(key, JSON.stringify(items))`, and re-renders. Consequences that
  are part of the contract: **duplicates are allowed** (no de-duplication, no quantity),
  order is append order, and adding never navigates anywhere.
- `data-item` strings are self-describing plain text in the format
  `"<Item> — <Store short label> — <Price/unit>[ <qualifier>]"` — the existing example,
  preserved exactly: `"Goat Mix Tray — Al Aqsa — $5.99/lb weekend"`. New rows follow the
  same format with §3.4 short labels (e.g. `"Chicken drumsticks — World Food Warehouse —
  $0.79/lb Mon–Thu"`).
- **Render:** `render()` reads the array and fills `<ul id="list">` with one `<li>` per
  string; when empty it renders exactly `<li class="muted">Nothing added yet.</li>`.
  `render()` runs once on load and after every mutation.
- **Clear:** the `#clear` button calls `localStorage.removeItem(key)` (removes the key
  entirely — it does not save `[]`) and re-renders.

**Porting rules for the builder:**

1. Key, format, parse/save code path, empty-state string, and clear semantics are
   **copied, not rewritten**. A list saved under the old page must render identically
   under the new page (acceptance §9).
2. **Extension — add buttons everywhere §5 names them:** the current page binds handlers
   with `querySelectorAll('.add[data-item]')` at load. Keep that exact binding strategy
   (it automatically covers every new row, including rows inside hidden views — hidden
   is not `display:none` removal from the DOM for binding purposes; the elements exist
   at parse time, so binding works). Do not switch to event delegation and do not bind
   lazily on view-open; the simple load-time binding is the contract.
3. **Extension — feedback:** on add, the clicked button's label changes to "Added" for
   1.2 seconds, then reverts ("Add", or "Add to my list" on the hero). No toast, no
   navigation, no focus move. The My List tab badge (§3.2) updates via `render()`.
4. **Extension — per-item remove (D6):** `render()` additionally appends the §5.8 Remove
   button to each `<li>`, carrying the item's **array index**. Clicking it splices that
   one index, re-saves via the same `setItem(key, JSON.stringify(items))` path, and
   re-renders. If the splice empties the list, the key is **kept** with `[]` (remove is
   not clear — only `#clear` removes the key). Item strings already in storage are never
   rewritten, re-formatted, or escaped differently by the new render.
5. The `#clear` button keeps class `add` but **no** `data-item` — it must never match
   the add binding. (True on the current page; the builder must not break it when
   restyling.)

### 7.5 Deep links and history

- On `DOMContentLoaded`, the router reads `location.hash` once and opens the §3.3
  target (default This Week). It also registers a `hashchange` listener with the same
  handler, which covers: pasted/shared links while the app is open, store-row anchor
  clicks, and browser Back/Forward through hashes.
- **Tab clicks update the hash** to the view's canonical hash (§3.1) using
  `history.pushState(null, '', '#<canonical>')` followed by the same `showView` call —
  so Back walks the user's view history (This Week → Stores → detail → Back returns to
  Stores index → Back returns to This Week). Opening a store detail pushes `#<slug>`;
  the Back button (§5.4) calls `history.back()` when the detail was reached in-app, and
  falls back to `showView('stores')` + setting the hash to `#stores` when it was the
  entry point (deep link with no in-app history). This is the "cheap" level of history
  friendliness and it is required, not optional.
- `pushState` does not fire `hashchange`; the router must not double-run (guard:
  `showView` is idempotent — calling it for the already-visible view/detail is a no-op,
  §7.1).
- Unknown or malformed hashes resolve to This Week (§3.3). The router never throws on a
  hash it does not recognise, including hashes with characters outside `[a-z-]`.
- **No view state is persisted.** Reloading with no hash always lands on This Week,
  whatever view was open last. The only persisted things in the whole app remain the
  two existing `localStorage` keys: `hb-theme` and `haulboard-list-v1`.

---

## 8. Regenerability — marker convention for the Friday run

The Friday cron rebuilds this page weekly from RUNBOOK.md / OPERATIONS.md. The app shell
(header, tab bar, scripts, styles) is **frozen**; weekly data changes only inside paired
HTML comment markers. A future agent swaps data between markers without touching the
shell — if a run ever needs to edit outside a marker pair, that is a design change and
goes through this spec, not the weekly run.

### 8.1 The convention (exact)

- Markers are HTML comments, **alone on their own line**, at column 0 inside the element
  they delimit, in exactly these forms:
  - View: `<!-- VIEW: <slug> -->` … `<!-- /VIEW: <slug> -->`
  - Section within a view: `<!-- SECTION: <name> -->` … `<!-- /SECTION: <name> -->`
  - Store detail block: `<!-- STORE: <slug> -->` … `<!-- /STORE: <slug> -->`
  - Inline data region (short text that changes weekly, e.g. the header week stamp):
    `<!-- DATA: <name> -->` … `<!-- /DATA: <name> -->`
  - Frozen shell block: `<!-- SHELL: <name> -->` … `<!-- /SHELL: <name> -->`
- Slugs/names are lowercase-hyphen and match §3.4 slugs and §3.1 view ids exactly.
- Pairs never cross: a `SECTION` pair sits wholly inside one `VIEW` pair; a `STORE` pair
  wholly inside the stores view's detail region; pairs do not nest inside themselves.
- Replacement rule: a run replaces everything **between** the marker lines, never the
  marker lines themselves, and never reorders markers. If a marker is missing at build
  time, the run fails loudly in its summary (per OPERATIONS.md failure rules) rather
  than guessing an insertion point.
- `SHELL` pairs (`header`, `tabbar`, `scripts-core`) delimit code the weekly run must
  never edit. The `<style>` block carries `/* SHELL: styles — edit only via UX spec */`
  as its first comment line.

### 8.2 Marker registry (complete)

| Marker | Lives in | Weekly content swapped |
|---|---|---|
| `DATA: week-stamp` | Header (§5.1) | "As of <date>" stamp |
| `DATA: plan` | Before `SHELL: scripts-core` | `window.HB_PLAN` JSON (stamp, per-item alternative prices for the savings meter, coupon/free-food counts) — generated by `plan_data.py` (v4) |
| `VIEW: this-week` | `<main>` | Whole view shell is structural; data inside its SECTIONs |
| `SECTION: lede` | This Week | Eyebrow date, pill counts ("<n> prices filed") |
| `SECTION: headline-deal` | This Week | Hero deal card contents + its `data-item` |
| `SECTION: top-picks` | This Week | Top picks deal rows |
| `SECTION: festival-pantry` | This Week | Festival card, or **removed entirely** when no festival is live (section markers go with it; the run re-adds them from this registry when a festival starts) |
| `VIEW: stores` | `<main>` | Structural; data inside SECTIONs and STOREs |
| `SECTION: store-index` | Stores | Featured rows, three groups' rows, teasers, statuses, watchlist closing line |
| `SECTION: store-details` | Stores (detail sub-view) | Wrapper for all STORE pairs |
| `STORE: <slug>` × 15 (§3.4) | Inside `store-details` | One store's meta line, status note, deal rows, flyer-link slot |
| `VIEW: compare` | `<main>` | Structural; data inside SECTIONs |
| `SECTION: price-compare-intro` | Compare | Intro line (count + date) |
| `SECTION: price-table-master` | Compare | Master table body |
| `SECTION: price-table-<category>` × 8 | Compare | One pair per category, named `price-table-meat-poultry`, `price-table-produce`, `price-table-other-groceries`, `price-table-dairy-frozen-breads`, `price-table-grains-rice-flour`, `price-table-oils-ghee`, `price-table-spices-masalas`, `price-table-dals-beans` |
| `SECTION: sample-basket` | Compare | Basket total line |
| `VIEW: coupons` | `<main>` | Structural |
| `SECTION: clip-checklist` | Coupons (v4) | "Clip before Saturday" rows — digitals behind the week's deals; checkbox state is on-device (`hb-clip-v1`) |
| `SECTION: coupons-shortlist` | Coupons | Top 5 + per-store coupon rows, or the single "lands with the Friday run" line |
| `SECTION: coupons-notes` | Coupons | Standing notes (changes rarely; still marker-delimited) |
| `VIEW: free-food` | `<main>` | Structural |
| `SECTION: free-food-list` | Free Food | Intro card + organisation rows (dates change weekly) |
| `VIEW: my-list` | `<main>` | **Static shell — no weekly data.** `<ul id="list">` is filled by JS at runtime. The run must not write list items into the HTML. Since v4 this is the "Plan my haul" planner (grouped totals, qty steppers, savings meter); its only weekly input is the `DATA: plan` block |
| `SHELL: header` / `SHELL: tabbar` / `SHELL: scripts-core` | Body/scripts | Never edited by a weekly run |

The current page already ends its body with three separate `<script>` blocks (list,
service-worker registration, theme). The redesign keeps them as separate blocks in the
same roles, adds the router script, and wraps the set in the `scripts-core` shell
markers. The service-worker registration snippet is unchanged.

---

## 9. Acceptance checklist (for the critique agent)

Score each item pass/fail against the built page. Any fail on a ★ item blocks shipping.

**Structure and views**
- ★ Exactly one view visible at a time at 390×844 and at 1280×800; hidden views carry
  the `hidden` attribute.
- ★ Bottom tab bar on mobile: six tabs in §3.1 order, fixed, clears content with the
  §3.2 bottom padding, respects `env(safe-area-inset-bottom)`.
- ★ Desktop ≥761px: the same nav renders as a top pill bar under the header; no bottom
  bar; `.wrap` max-width is 1100px.
- Slim header (56px) on every view with wordmark, week stamp, and `#themeToggle`.
- Store detail is a distinct screen; Stores tab remains selected while it is open;
  Back button and active-Stores-tab tap both return to the index.

**Content completeness (audit against §4)**
- ★ Every priced item on the old page appears in the new page (split rows count);
  the §4 audit rule's visible-copy diff shows only permitted changes.
- ★ All 9 compare tables present, same headings, same row counts, same order.
- Headline deal: hero treatment intact; its `data-item` string byte-identical to the
  old page.
- Watchlist group contains all three watchlist-only stores + the closing Flipp line;
  Meat Grinder text preserved.
- Footer with all three links present at the bottom of This Week.

**Deep links**
- ★ All ten legacy hashes from §3.3 open the mapped view (and store detail / scroll
  target where specified); `#compare`, `#stores`, and all 15 store slugs work; an
  unknown hash opens This Week without errors.
- Back/Forward move through opened views and details per §7.5.

**Density and targets**
- ★ No interactive element under 44px in its tap dimension (measure Add, Remove, Clear,
  Back, theme toggle, tabs, store rows).
- ★ Deal prices render at 18px/800; no horizontal page scroll at 360px width (Compare
  tables scroll only inside their `.tw` regions).
- Type sizes match the §6.1 table (spot-check H1, view title, item name, meta, table).

**Contracts preserved**
- ★ Theme: with `hb-theme=dark` set before load, the page opens dark; toggling flips
  label and persists to the same key; CSS variables match §1 table in both themes.
- ★ My List: seed `localStorage` key `haulboard-list-v1` with
  `["Goat Mix Tray — Al Aqsa — $5.99/lb weekend"]` before load — the item appears in
  My List and the tab badge shows 1. Adding from a store detail appends a correctly
  formatted string; duplicates are allowed; Remove deletes exactly one entry; Clear
  removes the key; empty state reads "Nothing added yet."
- ★ Add buttons are bound and functional inside hidden-then-opened views without
  rebinding (§7.4 rule 2).
- Tab bar My List badge updates on add, remove, and clear, and hides at zero.

**Regenerability and constraints**
- ★ Every marker in the §8.2 registry is present, correctly paired, and alone on its
  line; no weekly data (dates, counts, prices) appears outside a marker pair.
- ★ `sw.js` differs from the old file only in `CACHE = 'haulboard-v3'` and the "shell
  v3" comment; `manifest.webmanifest` is byte-identical to the old file.
- ★ Privacy scan passes: no phone-number patterns, no owner name, no home address in
  the publish set; free-food view lists no phone numbers.
- Single file, no external URLs in CSS/JS/fonts (repo/docs links in the footer
  excepted, as now); `index.html` well under 1 MB.
- Accessibility spot-checks: tabs expose `aria-selected`; arrow keys move between tabs;
  deep-link open moves focus to the view title; Compare table wrappers are focusable
  regions with labels; `prefers-reduced-motion` disables transitions.

---

## 10. Out of scope for this build

- No cart building, checkout, quantities, or totals in My List (still the roadmap's
  Phase 1 — the note trim in §4 row 28 already reflects that it is not this build).
- No search, filters, or sorting in any view. Compare is a lookup table, not a query
  tool; if a filter is ever added it gets its own spec.
- No new pages, no change to the in-app dashboard edition, no change to `data/` files,
  the price database, or the Friday run's capture/compare steps — this spec governs
  only the GitHub Pages `index.html` presentation layer and the two build items named
  in §1.7–1.8.
- No icon set, illustration, or imagery. The current page has none; Market Ledger here
  is type, colour, and rules (dividers), and that is sufficient.
