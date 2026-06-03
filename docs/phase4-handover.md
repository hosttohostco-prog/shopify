# Phase 4 Handover — Category Page

**Status:** Complete and approved (2026-06-03). Pushed to `hosttohostco-prog/shopify` `main`.

---

## Work Completed

The vanilla Dawn 2-section `collection.json` was replaced with the approved 5-section category page:

1. **`collection-hero-editorial`** — editorial hero with breadcrumb, collection image + scrim, metafield-driven kicker/subtitle/caption (fallback settings when empty)
2. **`collection-support` (before grid)** — Compact Mafe Note (`note` block) + Compact Featured Product (`featured` block)
3. **`main-collection-product-grid`** — Dawn commerce layer untouched; shead header (kicker + `<em>` title + product count + view-all link) added above `{%- paginate -%}`
4. **`collection-support` (after grid)** — WhatsApp CTA block
5. **`collection-list` (more-categories)** — 2-col uniform layout; `bano` + `ambiente` collection blocks wired directly in JSON; 3:2 landscape aspect ratio forced via `aspect-ratio: 3/2 !important` + `content: none` on `::before` (beats Dawn's inline `--ratio-percent`); cream background (`scheme-1`)

Additional UI work done this phase:
- Product grid cards: removed border/shadow, rounded image corners (12px), minimal layout
- Hover CTA: "Add to cart" pill repositioned inside the image frame (bottom-right), frosted-glass outlined pill, fade transition — product info always visible beneath
- Mafe Note avatar: inline SVG two-person icon fallback (no image upload required)
- Mafe Note `em` text: coral Shantell Sans override via `!important` (beats Dawn `component-rte.css` cascade)
- Category strip: removed entirely per James

---

## Files Created

| File | Purpose |
|---|---|
| `sections/collection-hero-editorial.liquid` | Editorial hero with breadcrumb + metafield-driven copy |
| `sections/collection-support.liquid` | Multi-block section: `note` / `featured` / `cta` block types |
| `snippets/breadcrumb.liquid` | Reusable Archivo-caps breadcrumb (collection + product contexts) |

---

## Files Modified

| File | Change |
|---|---|
| `templates/collection.json` | Full recompose — 5 sections; `bano`/`ambiente` blocks wired; fallback note text with `<em>` wrapping |
| `sections/main-collection-product-grid.liquid` | Grid header (kicker + title + product count + link) added above paginate |
| `assets/brand.css` | Phase 4 CSS block: breadcrumb, hero, note, featured product, grid header, product card overrides, hover CTA pill, room cards (more-categories), CTA section |

---

## Open Admin / Content Tasks

These require manual action in Shopify Admin — no code changes needed.

### 1. Define 7 collection metafields
Admin → Content → Metafields → Collections:

| Metafield | Type | Used By |
|---|---|---|
| `custom.hero_kicker` | Single line text | Hero kicker label |
| `custom.hero_subtitle` | Single line text | Hero subtitle |
| `custom.hero_caption` | Single line text | Hero caption badge |
| `custom.mafe_note` | Multi-line text / rich text | Mafe Note quote |
| `custom.featured_product` | Product reference | Featured Product block |
| `custom.cta_text` | Single line text | WhatsApp CTA text |
| `custom.cta_link` | URL | WhatsApp CTA link override |

All 7 have graceful fallbacks — sections hide or use block-setting defaults when metafields are empty. Theme works without these defined; they unlock per-collection editorial customisation.

### 2. Upload collection images for Baño and Ambiente
Both `bano` and `ambiente` currently have `"image": null` per Shopify API. The more-categories cards will show a placeholder until images are added. Upload via Admin → Collections → [collection] → Collection image.

---

## Known Issues / Watch Points

- **Room card aspect ratio** — Uses `aspect-ratio: 3/2 !important` on `.card__inner.ratio` + `content: none` on `::before`. This is the correct approach for Dawn's inline `--ratio-percent` pattern; confirm visually on preview.
- **Dawn RTE em cascade** — The Mafe Note `em` override requires `!important` on three selector variants because `component-rte.css` loads after `brand.css`. This is intentional and correct.
- **Quick-add bottom offset** — The hover CTA is positioned `bottom: calc(70px + 18px)` to clear the info section. If card info height changes (e.g. vendor line enabled, rating shown), this value may need tuning.
- **`main-collection-banner`** — Still on disk, no longer referenced by `collection.json`. Phase 6 cleanup.

---

## Phase 5 Starting Point

**Goal:** Deliver the approved PDP while preserving Dawn product form, variants, media gallery, add-to-cart, and dynamic checkout.

**Approved page hierarchy (2026-06-03):**
1. Hero — breadcrumb + gallery + buy panel (kicker / title / subtitle / price / variants / qty / CTA / assurances / WhatsApp)
2. Mafe Note — standalone editorial block below the hero (moved out of buy panel to reduce above-the-fold density)
3. Trust Layer — sage bg, criteria from `product_criterion` metaobject list
4. Product Information — retained Dawn accordion blocks (Materials / Shipping & Returns / Dimensions / Care Instructions), restyled to brand system
5. Cross-sell rail — horizontal scroll, host quote per card, metafield-first data source

Sticky bar: fixed bottom, scroll-triggered, dispatches to main product form.

**Primary files to touch:**
- `templates/product.json` — full recompose; remove filler sections (`image-with-text`, `multicolumn`)
- `sections/main-product.liquid` — targeted additions only (breadcrumb, badge, kicker block, assurance row block); hero structure preserved
- `sections/related-products.liquid` — refactor to horizontal scroll rail
- `assets/brand.css` — Phase 5 CSS block (all overrides here per D4; `section-main-product.css` not edited)

**New files to create:**
- `sections/product-editorial.liquid` — two presets: `mafe_note` and `trust`
- `snippets/product-sticky-add.liquid` — fixed bottom bar + IntersectionObserver JS

**Data model required (create in Admin before or during Phase 5):**
- 5 product metafields: `custom.display_category`, `custom.curation_note`, `custom.curation_badge`, `custom.criteria`, `custom.cross_sell_products`
- 1 metaobject: `product_criterion` (fields: `heading`, `body`)
- `custom.sell_unit` already exists and is populated
- `custom.use_cases` / `product_use_case` metaobject deferred

**Convention reminders:**
- All CSS overrides → `brand.css` only (D4); do not edit Dawn component CSS without explicit approval
- Dawn 10px rem base: `html { font-size: 62.5% }` → 1rem = 10px; use `px` clamps for brand copy
- `breadcrumb.liquid` is already Phase-5-ready (handles product template context)
- Title `<em>` accent word is a known Shopify constraint — plain `product.title` is the implementation
- Sticky bar JS must dispatch to `product-form.js`, not submit a second form
