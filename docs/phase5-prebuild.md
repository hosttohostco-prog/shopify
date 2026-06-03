# Phase 5 Pre-Build Handover — Product Detail Page

**Date:** 2026-06-03  
**Status:** Phases 1–4 complete and pushed to `hosttohostco-prog/shopify` main. Phase 5 not started.

---

## Project Context

HostToHost is a Shopify OS 2.0 storefront built on Dawn 15.4.1. The theme uses Outfit / Archivo / Shantell Sans, a periwinkle-led palette, and `--coral-cta #c14d2e` as the primary CTA color. All brand overrides live in `assets/brand.css` (D4 convention — never edit Dawn component CSS without explicit approval). The rem base is 62.5% (1rem = 10px); use `px` clamps for brand copy.

Source of truth: `docs/build-status.md`. Architecture: `docs/architecture-map.md`. Data model: `docs/data-model-specification.md`.

---

## Approved Phase 5 Page Structure

```
1. Hero                     main-product section (existing, refactored)
   ├─ Breadcrumb            snippets/breadcrumb.liquid (built in Phase 4, product-context ready)
   ├─ Gallery               thumbnail strip layout; "Selección de Mafe" coral badge top-left
   └─ Buy panel
       ├─ Category kicker   custom.display_category (coral-deep Archivo uppercase)
       ├─ Product title      h1, plain text (no <em> accent — Shopify constraint, accepted)
       ├─ Subtitle           descriptors.subtitle standard metafield, Archivo uppercase
       ├─ Price             Outfit 500, 30px
       ├─ Variant selector  circle swatches (Dawn variant picker, already configured)
       ├─ Quantity stepper  restyled to cream pill (CSS only)
       ├─ Add-to-bag CTA    ink pill, "Agregar a la bolsa" (locale override needed)
       ├─ Assurance row     ✓ Entrega 24h · ✓ Cambios 30 días (new block or custom_liquid)
       └─ WhatsApp link     settings.whatsapp_link (global setting, already exists)

2. Mafe Note                product-editorial section · mafe_note preset
   └─ custom.curation_note  Shantell Sans coral-deep quote; periwinkle avatar

3. Trust Layer              product-editorial section · trust preset
   └─ custom.criteria       list of product_criterion metaobject refs (heading + body)
                            Sage background; green-deep criterion rows

4. Product Information      main-product accordion blocks (retained, restyled)
   ├─ Materials             collapsible_tab (existing Dawn block)
   ├─ Shipping & Returns    collapsible_tab (existing Dawn block)
   ├─ Dimensions            collapsible_tab (existing Dawn block)
   └─ Care Instructions     collapsible_tab (existing Dawn block)

5. Cross-sell rail          related-products section (refactored)
   ├─ Kicker + H2           "Explora el resto de la colección" / "Más piezas que ya probé"
   ├─ "Ver toda la tienda"  tlink with arrow
   └─ Horizontal scroll     product cards + Mafe host quote (custom.curation_note per card)
                            Data: custom.cross_sell_products metafield → recommendations API fallback

[Sticky bar]                snippets/product-sticky-add.liquid (fixed bottom, scroll-triggered)
   └─ Dispatches to main product-form.js — NOT a second form
```

---

## Architectural Decisions

| Ref | Decision |
|---|---|
| A1 | Mafe Note is a standalone section below the hero, not inside the buy panel (reduces above-the-fold density) |
| A2 | Accordion (Materials / Shipping / Dimensions / Care) is retained and restyled — not replaced by a specs-grid metafield |
| A3 | All CSS overrides in `brand.css` (D4). `section-main-product.css` is not edited. |
| A4 | Sticky bar JS dispatches to existing `product-form.js` add-to-cart — no second form |
| A5 | Title `<em>` accent word is not implemented — `product.title | escape` strips HTML; accepted constraint |
| A6 | Cross-sell: `custom.cross_sell_products` metafield first; Shopify recommendations API fallback |
| A7 | `show_dynamic_checkout: false` — approved design shows one CTA only |

---

## Required Metafields (create in Shopify Admin → Content → Metafields → Products)

| Metafield | Type | Used By |
|---|---|---|
| `custom.display_category` | Single line text | Buy panel kicker |
| `custom.curation_note` | Multi-line text | Mafe Note section + cross-sell card quotes |
| `custom.curation_badge` | Single line text | Gallery badge label (optional; defaults to "Selección de Mafe") |
| `custom.criteria` | List · metaobject reference (`product_criterion`) | Trust Layer rows |
| `custom.cross_sell_products` | List · product reference | Cross-sell rail |
| `custom.sell_unit` | Single line text | Pack unit label (**existing, already populated**) |

**Metaobject to create:** `product_criterion` — fields: `heading` (single_line_text), `body` (multi_line_text).

All metafields have graceful fallbacks — sections hide when empty.

---

## Deferred Items (not Phase 5)

- `custom.use_cases` / `product_use_case` metaobject — use case cards section
- Specs-grid metafields (`material`, `finish`, `capacity`, `contents`, `care`, `dimensions`) — content goes in accordion text instead
- Title `<em>` accent word
- Gallery thumbnail labels (Frente / Detalle / En uso / Set) — photography direction only, not functional UI

---

## Files to Modify

| File | Change |
|---|---|
| `templates/product.json` | Full recompose: remove `image-with-text` + `multicolumn` filler; set approved 5-section order; `show_dynamic_checkout: false`; `gallery_layout: thumbnail` |
| `sections/main-product.liquid` | Targeted additions: render `breadcrumb.liquid` above the grid; add gallery badge overlay; add `display_category` kicker block type; add assurance row block type |
| `sections/related-products.liquid` | Refactor: replace grid with horizontal scroll rail; add kicker/H2/view-all header; add Mafe quote overlay per card |
| `assets/brand.css` | Phase 5 CSS block: gallery thumbnail strip, buy panel (qty pill, swatch, CTA), Mafe Note card, Trust Layer (sage bg, criterion rows), accordion restyle, cross-sell rail, sticky bar |

---

## Files to Create

| File | Purpose |
|---|---|
| `sections/product-editorial.liquid` | Two presets: `mafe_note` (standalone quote block) and `trust` (sage-bg criteria loop). No metafields introduced beyond what is listed above. |
| `snippets/product-sticky-add.liquid` | Fixed bottom bar: product thumbnail + title + unit + price + "Agregar a la bolsa" pill. IntersectionObserver watches `#buy-anchor`; toggles `.on` class. |

---

## Key Implementation Constraints

1. **`<product-info>` custom element** — all of `main-product.liquid` lives inside this web component. `product-info.js` drives variant selection, URL updates, and cart AJAX. DOM structure inside it must not be disrupted. New elements (breadcrumb, badge) go outside or in new block slots.

2. **`product-form.js` owns add-to-cart** — the sticky bar must not submit a second `{% form 'product' %}`. Instead: read qty from `#Quantity-[section.id]`, then click the primary add button, or dispatch a custom event that `product-form.js` handles.

3. **Gallery layout** — changing `gallery_layout` from `stacked` to `thumbnail` in `product.json` is a schema-driven change. `media-gallery.js` is coupled to Dawn's gallery HTML; verify on Shopify preview after the change.

4. **`brand.css` load order** — `brand.css` loads before Dawn component CSS in some contexts. Use specificity (not `!important` unless necessary) for overrides. The Mafe Note `em` cascade fix from Phase 4 (`!important` on three selectors) is the established pattern for cases where Dawn's component CSS wins.

5. **No new JS files** — per project convention, custom JS should be minimal and inline in snippets (as with Phase 3B). The sticky bar observer is the only JS required this phase.

6. **Theme check baseline** — current baseline is 2 errors / 13 warnings (pre-existing, none in Phase 1–4 files). Phase 5 must not introduce new offenses.

---

## Known Risks

| Risk | Severity |
|---|---|
| Additions to `main-product.liquid` break `product-info.js` DOM expectations | High |
| Gallery layout change regresses media modal or zoom behavior | High |
| Sticky bar JS conflicts with product form state | High |
| `product_criterion` metaobject undefined in admin → Trust Layer untestable until created | Medium |
| Cross-sell `curation_note` empty on most products → cards show without quote | Medium |
| Accordion `collapsible_tab` restyle via `brand.css` may need `!important` due to `component-accordion.css` load order | Low |
