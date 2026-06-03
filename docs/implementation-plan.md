# HostToHost Implementation Plan

This is the source-of-truth delivery plan for moving the current Shopify build to the approved HostToHost storefront.

It uses `architecture-map.md` as the fixed architecture source of truth. Do not use this document to revisit architecture decisions or redesign the experience.

## Delivery Principles

- Preserve the current Dawn commerce architecture.
- Maximize reuse of the existing Shopify implementation.
- Implement the approved React designs with high fidelity.
- Keep merchant editability native to Shopify OS 2.0.
- Prefer existing assets before creating new ones.
- Build shared foundations before page-specific work.

## Part 1 — Current State Assessment

| Status | Summary |
|---|---|
| Already Complete | Dawn 15.4.1 fork exists and is stable. Shopify CLI/theme workflow has been established. Cart drawer, product forms, product media, variant handling, collection filtering, search, customer templates, localization utilities, and core commerce snippets are reusable. Header, footer, hero, featured collection, collection list, product card, newsletter, and several editorial homepage sections already exist in some form. `custom.sell_unit` exists and is populated. |
| Partially Complete | Homepage exists but reflects the earlier Fraunces/Variant-D implementation, not the approved Outfit/Archivo/Shantell Sans system. Header/footer/cart/card systems are structurally strong but visually misaligned. Product cards are functionally useful but need approved presentation. Collection and PDP templates use Dawn foundations but do not yet match approved category/PDP architecture. |
| Not Started | Approved global design-system migration, consolidated custom sections, category-page editorial architecture, product-page editorial architecture, new product/collection metafields, two PDP metaobjects, breadcrumb snippet, optional sticky add snippet, and final approved template composition. |

## Part 2 — Implementation Phases

| Phase | Objective | Existing Assets Reused | New Assets Required | Dependencies |
|---|---|---|---|---|
| Phase 1 | Establish approved global design foundation | `brand.css`, `theme.liquid`, `settings_schema.json`, `settings_data.json`, header/footer/cart/card CSS foundations | None required beyond settings changes | Approved React CSS tokens and `architecture-map.md` |
| Phase 2 | Align shared commerce components and global chrome | Header, footer, announcement bar, cart drawer, `card-product`, `card-collection`, price, variant, media snippets | Optional `approved-theme.js` only if needed | Phase 1 |
| Phase 3 | Deliver approved homepage | `hero`, `featured-collection`, `collection-list`, `newsletter-cartas`, footer/header | `home-editorial.liquid`, `product-showcase.liquid` | Phases 1-2 |
| Phase 4 | Deliver approved category page | `main-collection-product-grid`, `collection-list`, product cards, collection objects | `collection-hero-editorial.liquid`, `collection-support.liquid`, `breadcrumb.liquid`; collection metafields | Phases 1-2, shared card complete |
| Phase 5 | Deliver approved product page | `main-product`, product media, buy buttons, variant picker, price, related products | `product-editorial.liquid`, optional `product-sticky-add.liquid`; product metafields; two metaobjects | Phases 1-2, data model |
| Phase 6 | Integrate, QA, merchant-editability validation | Entire theme, existing Dawn commerce behavior, templates | No new architecture assets expected | Phases 1-5 |

## Part 3 — Detailed Workstreams

### Phase 1 — Approved Global Foundation

**Goal**

Move the theme from the earlier Fraunces/DM Sans/DM Mono/Caveat layer to the approved Outfit / Archivo / Shantell Sans, periwinkle-led system without disrupting commerce behavior.

| Category | Details |
|---|---|
| Files Modified | `assets/brand.css`, `layout/theme.liquid`, `config/settings_schema.json`, `config/settings_data.json` |
| New Files Created | None |
| Metafields Added | None |
| Metaobjects Added | None |
| Risks | Global CSS regression, font-loading mismatch, Dawn rem-base mismatch, color contrast regressions, unintended changes to customer/cart/search pages. |
| Testing Requirements | Theme check, homepage/category/PDP visual smoke test, header/footer/cart drawer check, mobile/desktop typography check, button/input/card regression check. |

### Phase 2 — Shared Commerce Components And Chrome

**Goal**

Bring reusable components into the approved design system before building page-specific sections, so downstream pages inherit the right foundation.

| Category | Details |
|---|---|
| Files Modified | `sections/header.liquid`, `sections/header-group.json`, `sections/announcement-bar.liquid`, `sections/footer.liquid`, `sections/footer-group.json`, `snippets/card-product.liquid`, `snippets/card-collection.liquid`, `snippets/product-media-gallery.liquid`, `assets/component-card.css`, `assets/component-price.css`, `assets/component-product-variant-picker.css`, `assets/component-cart-drawer.css`, `assets/section-footer.css`, `assets/section-collection-list.css` |
| New Files Created | Optional `assets/approved-theme.js` only if approved interactions need small non-commerce JS support |
| Metafields Added | Product `custom.display_category`, `custom.curation_note`, `custom.curation_badge`, `custom.short_spec` if needed for cards/PDP labels |
| Metaobjects Added | None |
| Risks | Product-card changes affect homepage, collections, search, related products, and cart recommendations. Header/footer styling can affect all pages. |
| Testing Requirements | Product card in featured collection, collection grid, related products; add-to-cart quick-add; cart drawer; header mobile drawer; footer menus; product price formatting. |

### Phase 3 — Approved Homepage

**Goal**

Convert the current partial homepage into the approved storefront homepage using consolidated sections and existing Shopify product/collection mechanics.

| Category | Details |
|---|---|
| Files Modified | `templates/index.json`, `sections/hero.liquid`, `sections/featured-collection.liquid`, `sections/collection-list.liquid`, `sections/newsletter-cartas.liquid` |
| New Files Created | `sections/home-editorial.liquid`, `sections/product-showcase.liquid` |
| Metafields Added | None required beyond Phase 2 product-card fields |
| Metaobjects Added | None |
| Risks | Overloading consolidated sections with too many layout modes; losing merchant clarity; accidentally hardcoding React data instead of using Shopify collections/products/blocks. |
| Testing Requirements | Homepage section order, section settings, block add/remove/reorder, product showcase add-to-cart, newsletter form success/error states, responsive hero/marquee/rooms/picks layouts. |

### Phase 4 — Approved Category Page

**Goal**

Turn `collection.json` from a generic Dawn collection template into the approved category experience while preserving filtering, sorting, pagination, and product-card reuse.

| Category | Details |
|---|---|
| Files Modified | `templates/collection.json`, `sections/main-collection-product-grid.liquid`, `assets/template-collection.css` |
| New Files Created | `sections/collection-hero-editorial.liquid`, `sections/collection-support.liquid`, `snippets/breadcrumb.liquid` |
| Metafields Added | Collection `custom.hero_kicker`, `custom.hero_subtitle`, `custom.hero_caption`, `custom.mafe_note`, `custom.featured_product`, `custom.cta_text`, `custom.cta_link` |
| Metaobjects Added | None |
| Risks | Collection metafields must gracefully fall back when empty. Product-grid changes must not break Shopify filters/sorting. Category strip should remain merchant-editable and not hardcoded to three categories. |
| Testing Requirements | Cocina/Baño/Ambiente collection pages, empty metafield fallback, filter/sort/pagination, product count, featured product binding, mobile category strip, more-categories behavior. |

### Phase 5 — Approved Product Page

**Goal**

Deliver the approved PDP hierarchy while preserving Dawn product form, variant, media, price, cart, and related-product behavior.

| Category | Details |
|---|---|
| Files Modified | `templates/product.json`, `sections/main-product.liquid`, `sections/related-products.liquid`, `assets/section-main-product.css` |
| New Files Created | `sections/product-editorial.liquid`, optional `snippets/product-sticky-add.liquid` |
| Metafields Added | Product `custom.material`, `custom.finish`, `custom.capacity`, `custom.contents`, `custom.care`, `custom.dimensions`, `custom.criteria`, `custom.use_cases`, `custom.cross_sell_products` |
| Metaobjects Added | `product_criterion`, `product_use_case` |
| Risks | Highest risk phase. Product form must remain valid. Variant state, dynamic checkout, media gallery, add-to-cart, and sticky add behavior can regress if layout refactor is too aggressive. Metaobject content must be usable by merchants. |
| Testing Requirements | All variants, add-to-cart, dynamic checkout, media gallery, mobile sticky add behavior if used, metafield fallbacks, accordion blocks, criteria/use-case rendering, related/cross-sell products. |

### Phase 6 — Integration And Release Validation

**Goal**

Validate the approved storefront as a coherent Shopify-native build and confirm merchant editability across homepage, category, and PDP.

| Category | Details |
|---|---|
| Files Modified | Template JSON defaults and settings data only as needed |
| New Files Created | None expected |
| Metafields Added | No new architecture fields expected |
| Metaobjects Added | No new architecture objects expected |
| Risks | Cross-page visual inconsistency, missing data causing blank sections, merchant settings becoming confusing, performance issues from new imagery/sections. |
| Testing Requirements | Theme check, storefront smoke test, Theme Editor editability pass, mobile/desktop screenshots, cart/checkout path, collection filtering, PDP purchase path, empty-data fallbacks, Shopify preview review. |

## Part 4 — Recommended Build Sequence

1. **Global design foundation first.**  
   This removes the old Fraunces-era design system and prevents every later section from being built twice.

2. **Shared product/card/header/footer components second.**  
   Product cards and global chrome appear everywhere. Stabilizing them early reduces homepage/category/PDP rework.

3. **Homepage third.**  
   It has the most visible approved-design impact and reuses the shared card, header, footer, collection list, newsletter, and product showcase foundations.

4. **Category page fourth.**  
   Category depends heavily on the shared product card and collection-card work. It also introduces collection metafields before PDP's heavier data model.

5. **Product page fifth.**  
   PDP is the highest risk because it touches product form, media, variants, metafields, metaobjects, accordion content, related products, and optional sticky add behavior.

6. **Integration QA last.**  
   Once all templates are using the approved system, validate merchant editability and commerce behavior together.

This order keeps the riskiest commerce refactor until the shared visual and data foundations are already stable.

## Part 5 — Quick Wins

| Quick Win | Why It Matters |
|---|---|
| Replace global typography and palette tokens | Immediate visible alignment with approved designs and unlocks all later work. |
| Align header/footer/announcement bar | Low-risk global progress with strong storefront impact. |
| Refactor product card visual layer | High reuse: homepage, category grid, related products, and product showcases all benefit. |
| Update `featured-collection` and `collection-list` styling | Uses existing sections and quickly improves homepage fidelity. |
| Add collection metafields | Low technical risk and unlocks approved category hero/note/featured product. |
| Add product metafields for specs/notes | Unlocks PDP content without touching product form logic. |

## Part 6 — Critical Path

| Critical Work | Type | Why It Is Critical |
|---|---|---|
| Global approved design layer | Blocks all visual implementation | Every section depends on correct tokens, fonts, spacing, and colors. |
| Shared product card refactor | Blocks homepage/category/cross-sell fidelity | Product presentation is reused across most commerce surfaces. |
| `product-showcase.liquid` | Blocks homepage picks and category featured product | Consolidated section is reused across multiple templates. |
| Collection metafields | Blocks category page fidelity | Category hero, note, CTA, and featured product require collection-level data. |
| `product-editorial.liquid` + product data model | Blocks approved PDP | Trust criteria, use cases, specs, and accordion all depend on this. |
| Metaobjects for PDP criteria/use cases | Longest-lead data modeling | Content structure and merchant workflows need to be right before PDP completion. |
| `main-product` refactor | Highest-risk theme work | Must preserve product form, variant state, media gallery, and add-to-cart behavior. |

Highest-risk work: `main-product` PDP refactor.

Longest-lead work: product data model, metaobjects, and merchant content population.

Most downstream-blocking work: global design layer and shared product card.

## Part 7 — Final Delivery Roadmap

### Phase 1

Approved global design foundation is in place. Theme uses the approved typography, color system, spacing, and core visual tokens. Existing commerce behavior remains unchanged.

### Phase 2

Shared storefront components are aligned: header, announcement bar, footer, cart drawer styling, product cards, collection cards, price treatment, and variant presentation.

### Phase 3

Homepage matches the approved React design using Shopify-native sections, consolidated editorial modules, product showcases, collection data, and editable newsletter mechanics.

### Phase 4

Category page is approved-design faithful: editorial hero, category strip, Mafe note, featured category product, product grid, contextual CTA, and more-category navigation are merchant-editable.

### Phase 5

Product page is approved-design faithful: gallery/buy panel, product note, trust layer, use cases, specs grid, retained accordion, cross-sell, and optional sticky add are powered by Shopify-native product data.

### Phase 6

Final storefront is validated end-to-end: Theme Editor editability, desktop/mobile fidelity, product purchase path, cart drawer, collection filtering, dynamic-source fallbacks, and Shopify theme checks are complete.
