# HostToHost Shopify Architecture Map

This is the source-of-truth architecture reference for implementing the approved HostToHost designs in the current Shopify Online Store 2.0 theme.

It consolidates the architecture inventory, design-to-Shopify mapping, Shopify OS 2.0 validation, theme impact assessment, and simplification review. It keeps only final decisions.

## Core Decision

The current Shopify theme should not be rebuilt.

Use the existing Dawn 15.4.1 fork as the commerce foundation, preserve Shopify-native behavior, and implement the approved designs through a consolidated set of OS 2.0 sections, blocks, dynamic sources, metafields, and a small number of metaobjects.

The approved designs are the source of truth:

- Typography: Outfit, Archivo, Shantell Sans
- Palette: periwinkle-led with cream canvas and warm accents
- Pages in scope: homepage, category page, product page
- Goal: implementation fidelity with maximum reuse

## Architecture Priorities

Apply this order before creating any new asset:

1. KEEP existing Shopify/Dawn behavior where it is already correct.
2. EXTEND existing sections/snippets where the structure is sound.
3. REFACTOR existing presentation when the old Fraunces-era design conflicts with approved designs.
4. CREATE NEW only when an existing asset cannot maintain approved fidelity or merchant usability.

Use Shopify primitives in this order:

1. Sections
2. Blocks
3. Dynamic sources
4. Metafields
5. Metaobjects

Avoid hardcoded page content wherever possible.

## Keep As Foundation

These systems are fundamentally sound and should remain the functional backbone:

| Area | Final Decision |
|---|---|
| Shopify theme base | Keep Dawn 15.4.1 fork. |
| Cart drawer and cart behavior | Keep existing Shopify-native behavior. |
| Product forms and add-to-cart | Keep Dawn product form logic. |
| Product media and variant behavior | Keep Dawn media gallery, variant picker, swatches, and product JS. |
| Collection filtering/sorting/pagination | Keep Dawn collection grid logic. |
| Search, customer, localization, utility templates | Keep unchanged unless later design scope expands. |
| Product cards | Keep the shared snippet pattern, refactor presentation. |
| Header/footer | Keep global OS 2.0 chrome, extend styling and defaults. |

## Reduced Custom Section Architecture

The custom section set should be minimized. The earlier one-section-per-approved-module approach is intentionally consolidated.

| Final Section | Type | Purpose |
|---|---|---|
| `home-editorial.liquid` | New consolidated section | Handles homepage marquee, founder/why story, selection rules, and simple editorial home modules through presets and blocks. |
| `product-showcase.liquid` | New consolidated section | Handles Mafe picks, category featured product, curated product rails, product grids, and dark showcase variants using shared product cards. |
| `collection-hero-editorial.liquid` | New section | Dedicated approved category hero using collection data and collection metafields. |
| `collection-support.liquid` | New consolidated section | Handles category strip, product count display, Mafe category note, contextual CTA, and related/more category support blocks. |
| `product-editorial.liquid` | New consolidated section | Handles PDP trust criteria, use cases, specs grid, and optional purchase-support accordion. |
| `collection-list.liquid` | Existing section extension | Handles homepage rooms, future collections, and more-categories where collection-card behavior is sufficient. |

Net decision: use 5 new custom sections plus targeted extensions to existing sections. Do not create separate sections for every approved module.

## Approved Design Mapping

### Homepage

| Approved Section | Shopify Architecture | Final Decision |
|---|---|---|
| Announcement bar | Existing `announcement-bar` | EXTEND |
| Navigation | Existing `header` | EXTEND |
| Hero | Existing `hero` refactored to approved system | REFACTOR |
| Marquee | `home-editorial` preset/block layout | CREATE NEW via consolidated section |
| Categories / rooms | Extended `collection-list` | EXTEND |
| Why HostToHost exists | `home-editorial` image/story layout | CREATE NEW via consolidated section |
| Selection rules | `home-editorial` repeatable rule blocks | CREATE NEW via consolidated section |
| Featured products / Piezas para empezar | Existing `featured-collection` + `card-product` | EXTEND |
| Product card | Existing `card-product` snippet | REFACTOR |
| Mafe's Picks | `product-showcase` rail/dark layout | CREATE NEW via consolidated section |
| Insights / Notas de anfitriona | Blog/article section or future metaobject-backed editorial section | EXTEND later if used |
| Future collections | Extended `collection-list` or `home-editorial` block layout | EXTEND / CREATE via consolidated section only if fidelity requires |
| Signup | Existing newsletter mechanics, approved layout | REFACTOR |
| Footer | Existing `footer` | EXTEND |

### Category Page

| Approved Section | Shopify Architecture | Final Decision |
|---|---|---|
| Breadcrumb | Shared `breadcrumb` snippet | CREATE NEW snippet |
| Category hero | `collection-hero-editorial` | CREATE NEW |
| Category strip / pills | `collection-support` block layout | CREATE NEW via consolidated section |
| Product count | Collection object inside `collection-support` or grid header | EXTEND |
| Mafe category note | `collection-support` note block bound to collection metafields | CREATE NEW via consolidated section |
| Featured category product | `product-showcase` with collection-derived product source | CREATE NEW via consolidated section |
| Product grid | Existing `main-collection-product-grid` | EXTEND |
| Product card | Existing `card-product` | REFACTOR |
| Grid footer / WhatsApp CTA | `collection-support` CTA block | CREATE NEW via consolidated section |
| More categories | Extended `collection-list` or `collection-support` collection blocks | EXTEND |
| Footer | Existing `footer` | EXTEND |

### Product Page

| Approved Section | Shopify Architecture | Final Decision |
|---|---|---|
| Breadcrumb | Shared `breadcrumb` snippet | CREATE NEW snippet |
| Product gallery | Existing `main-product` / product media snippets | EXTEND |
| Gallery badge / Mafe pick label | Product metafield displayed in `main-product` or `product-editorial` | EXTEND |
| Buy panel | Existing `main-product` with approved layout | REFACTOR |
| Product category, subtitle, sell unit | Product type/metafields in `main-product` and card snippet | EXTEND |
| Variant swatches | Existing variant picker/swatches | EXTEND |
| Quantity + add to bag | Existing quantity/buy button/product form logic | EXTEND |
| Mafe product note | Product metafield rendered in PDP layout | EXTEND |
| Trust layer / criteria | `product-editorial` with criteria references | CREATE NEW via consolidated section |
| Use cases | `product-editorial` with use-case references | CREATE NEW via consolidated section |
| Specs grid | `product-editorial` or `main-product` block using product metafields | CREATE NEW via consolidated section |
| Purchase-support accordion | `product-editorial` accordion blocks, positioned after specs and before cross-sell | CREATE NEW via consolidated section |
| Cross-sell rail | Existing `related-products` or `product-showcase` using shared cards | REFACTOR / EXTEND |
| Sticky add bar | Snippet tied to Shopify product form state | CREATE NEW snippet only if approved PDP requires it |
| Footer | Existing `footer` | EXTEND |

## PDP Accordion Decision

The approved PDP removes the information accordion visually, but the content is useful and should be retained as secondary purchase-support content.

Final decision:

| Question | Decision |
|---|---|
| Retain accordion? | Yes. |
| PDP hierarchy | After approved specs/details grid and before cross-sell. |
| Implementation | As accordion blocks inside `product-editorial.liquid`, or existing `main-product` collapsible blocks only if placement remains correct. |
| Content source | Blocks for structure; dynamic sources, product metafields, or page references for content. |
| Avoid | Hardcoded accordion items in `product.json`. |

Accordion content model:

| Item | Preferred Source |
|---|---|
| Materials | Product metafield |
| Shipping & Returns | Page reference or editable rich text block |
| Dimensions | Product metafield |
| Care Instructions | Product metafield |

## Template Impact

### `templates/index.json`

Current structure:

`hero → collection_list → manifesto → featured_collection → editorial_split → favorite_product → newsletter`

Final required structure:

- Keep/refactor `hero`
- Keep/extend `collection_list` for rooms/categories where suitable
- Keep/extend `featured_collection`
- Keep/refactor newsletter
- Replace old `manifesto`, `editorial_split`, and `favorite_product` usage where they conflict with approved design
- Add consolidated home modules from `home-editorial` and `product-showcase`

Sections added:

- `home-editorial`
- `product-showcase`

Sections removed or no longer used:

- `manifesto`, unless repurposed is explicitly preferred later
- `editorial_split`, unless refactored into the consolidated home editorial pattern
- `favorite_product`, if superseded by `product-showcase`

### `templates/collection.json`

Current structure:

`main-collection-banner → main-collection-product-grid`

Final required structure:

- `collection-hero-editorial`
- `collection-support` for strip/note/CTA support blocks
- `product-showcase` for featured category product
- `main-collection-product-grid`
- Extended `collection-list` or `collection-support` for more categories

Sections removed or replaced:

- Replace `main-collection-banner` with `collection-hero-editorial` for approved category pages.

### `templates/product.json`

Current structure:

`main-product → image-with-text → multicolumn → related-products`

Final required structure:

- Refactored `main-product`
- `product-editorial`
- Refactored/extended `related-products` or `product-showcase`

Sections removed:

- Generic `image-with-text`
- Generic `multicolumn`

Sections added:

- `product-editorial`
- Optional `product-showcase` if replacing related products with approved cross-sell rail

## Existing Files To Modify

These files require presentation or structural changes to achieve approved design fidelity.

| File | Action | Reason |
|---|---|---|
| `assets/brand.css` | Major Refactor | Replace Fraunces-era design layer with approved Outfit / Archivo / Shantell Sans and periwinkle-led system. |
| `layout/theme.liquid` | Minor Refactor | Support approved font loading and global asset references. |
| `config/settings_schema.json` | Extension | Add or expose global approved design settings where merchant-editable. |
| `config/settings_data.json` | Extension | Update default values for approved schemes, typography, layout, and section defaults. |
| `sections/hero.liquid` | Major Refactor | Existing hero is only partially aligned and must match approved hero. |
| `sections/newsletter-cartas.liquid` | Major Refactor | Keep customer form mechanics; align with approved signup design. |
| `sections/main-product.liquid` | Major Refactor | Preserve commerce logic while refactoring PDP hierarchy. |
| `sections/related-products.liquid` | Major Refactor | Align cross-sell presentation or hand off to `product-showcase`. |
| `sections/main-collection-product-grid.liquid` | Extension | Keep grid/filter logic; align approved category presentation. |
| `sections/featured-collection.liquid` | Extension | Keep collection logic; align approved homepage product section. |
| `sections/collection-list.liquid` | Extension | Use collection blocks for rooms/future/more-categories where suitable. |
| `sections/header.liquid` | Extension | Align global chrome with approved type/palette/spacing. |
| `sections/footer.liquid` | Extension | Align footer with approved type/palette/spacing. |
| `sections/announcement-bar.liquid` | Extension | Align announcement bar with approved treatment. |
| `snippets/card-product.liquid` | Major Refactor | Keep product-card logic; align approved card content hierarchy and visual system. |
| `snippets/card-collection.liquid` | Extension | Align collection cards with approved rooms/category cards. |
| `snippets/product-media-gallery.liquid` | Extension | Align product gallery presentation while preserving behavior. |
| `assets/component-card.css` | Extension | Support approved product/collection card styling. |
| `assets/component-price.css` | Minor Refactor | Align price typography with approved system. |
| `assets/component-product-variant-picker.css` | Extension | Align variant picker/swatches with approved PDP. |
| `assets/section-main-product.css` | Major Refactor | Align PDP layout and approved product hierarchy. |
| `assets/template-collection.css` | Extension | Align collection grid spacing and presentation. |
| `assets/section-collection-list.css` | Extension | Align collection card/list layouts. |
| `assets/section-footer.css` | Extension | Align footer presentation. |
| `assets/component-cart-drawer.css` | Minor Refactor | Visual alignment only; behavior remains unchanged. |

## Existing Files To Leave Untouched

Leave the following groups unchanged unless later implementation reveals a direct conflict:

| File Group | Reason |
|---|---|
| Cart behavior assets: `cart.js`, `cart-drawer.js`, `cart-notification.js` | Existing Shopify cart behavior is sound. |
| Product commerce assets: `product-form.js`, `product-info.js`, `price-per-item.js`, `quantity-popover.js`, `pickup-availability.js` | Preserve Shopify-native product behavior. |
| Search/filter assets: `facets.js`, `predictive-search.js`, `search-form.js`, `main-search.js` | Existing behavior is reusable. |
| Customer/localization assets: `customer.js`, `localization-form.js` | Outside approved page scope. |
| Product media behavior assets: `media-gallery.js`, `product-modal.js`, `product-model.js`, `magnify.js` | Preserve media behavior. |
| Core commerce snippets: `price`, `unit-price`, `quantity-input`, `buy-buttons`, `product-variant-picker`, `product-variant-options`, `swatch`, `swatch-input`, `product-media`, `product-thumbnail` | Existing logic is correct; style/layout can be handled around them. |
| Utility snippets: `loading-spinner`, `pagination`, `facets`, `price-facet`, localization snippets, social icons, meta tags | Reusable utilities unaffected. |
| Non-impacted templates: customer templates, blog/article/search/cart/page/contact/404/password/gift-card templates | Outside current design implementation scope. |

## New Files To Create

Create only the reduced set below.

| New File | Type | Purpose |
|---|---|---|
| `sections/home-editorial.liquid` | Section | Consolidated homepage editorial section for marquee, why/founder, selection rules, and simple approved editorial modules. |
| `sections/product-showcase.liquid` | Section | Consolidated product showcase for Mafe picks, category featured product, curated rails/grids, and optional cross-sell variants. |
| `sections/collection-hero-editorial.liquid` | Section | Approved category hero driven by collection data/metafields. |
| `sections/collection-support.liquid` | Section | Consolidated category strip, Mafe note, CTA, count, and related category support. |
| `sections/product-editorial.liquid` | Section | Consolidated PDP criteria, use cases, specs grid, and accordion support. |
| `snippets/breadcrumb.liquid` | Snippet | Reusable category/PDP breadcrumb. |
| `snippets/product-sticky-add.liquid` | Snippet | Optional approved PDP sticky add bar tied to product form behavior. |
| `assets/approved-theme.js` | Asset | Lightweight approved-design progressive enhancement only if existing Dawn JS cannot cover marquee/reveal/sticky UI needs. |

## Metafields

Use metafields for product- and collection-specific facts. Do not create metafields for simple page-specific section copy that can live in section settings/blocks.

### Product Metafields

| Metafield | Purpose | Used By |
|---|---|---|
| `custom.sell_unit` | Existing sell-unit display. | Product cards, PDP buy panel, showcase sections. |
| `custom.display_category` | Customer-facing category label when product type is insufficient. | Product cards, PDP buy panel, category grids. |
| `custom.curation_note` | Mafe/product selection note. | PDP note, product card note if enabled. |
| `custom.curation_badge` | Badge such as "Selección de Mafe". | Product cards, PDP gallery badge. |
| `custom.short_spec` | Short PDP subtitle/spec line. | PDP buy panel. |
| `custom.material` | Product material fact. | PDP specs grid and accordion. |
| `custom.finish` | Product finish/acabado. | PDP specs grid. |
| `custom.capacity` | Product capacity where relevant. | PDP specs grid. |
| `custom.contents` | Set/pack contents. | PDP specs grid and sell-unit support. |
| `custom.care` | Care instructions. | PDP specs grid and accordion. |
| `custom.dimensions` | Dimensions. | PDP specs grid and accordion. |
| `custom.criteria` | List of product criterion metaobject references. | PDP trust layer. |
| `custom.use_cases` | List of product use-case metaobject references. | PDP use cases. |
| `custom.cross_sell_products` | Optional manual product recommendations. | PDP cross-sell/product showcase. |

### Collection Metafields

| Metafield | Purpose | Used By |
|---|---|---|
| `custom.hero_kicker` | Category hero kicker. | `collection-hero-editorial`. |
| `custom.hero_subtitle` | Category hero subtitle/line. | `collection-hero-editorial`. |
| `custom.hero_caption` | Category hero image caption. | `collection-hero-editorial`. |
| `custom.mafe_note` | Collection-specific Mafe note. | `collection-support`. |
| `custom.featured_product` | Category-specific featured product. | `product-showcase`. |
| `custom.cta_text` | Category footer/support CTA text. | `collection-support`. |
| `custom.cta_link` | Category CTA/WhatsApp link. | `collection-support`. |

### Page Metafields

No page metafields are required for the approved homepage/category/PDP scope.

## Metaobjects

Be conservative. Use metaobjects only where a simpler metafield or section block is inadequate.

| Metaobject | Purpose | Referenced By | Justification |
|---|---|---|---|
| `product_criterion` | Repeatable PDP trust criteria: icon, title, body, order. | Product metafield `custom.criteria`; `product-editorial`. | Fixed metafield slots would be rigid, hard to reorder, and poor for future AI/content tooling. |
| `product_use_case` | Repeatable PDP use-case cards: image, title, note, order. | Product metafield `custom.use_cases`; `product-editorial`. | Use cases are variable-count image/text cards; simple metafields become unwieldy. |

Do not create metaobjects for homepage marquee items, selection rules, future collections, category CTAs, or simple notes. Use section blocks or collection/product metafields instead.

## Theme Settings Impact

Theme settings should support global approved-design consistency while avoiding excessive merchant controls.

| Setting | Scope | Purpose |
|---|---|---|
| Approved primary font | Typography | Outfit. |
| Approved label font | Typography | Archivo. |
| Approved hand font | Typography | Shantell Sans. |
| Periwinkle | Global color | Approved lead brand color. |
| Periwinkle deep | Global color | Hover/active/depth state. |
| Cream canvas | Global color | Approved page background. |
| Coral accent | Global color | Human/accent moments. |
| Orange accent | Global color | Secondary warm accent. |
| Olive / green deep | Global color | Trust/supporting tones. |
| Ink | Global color | Primary text and dark surfaces. |
| Approved max width | Layout | Shared page/section width. |
| Approved gutter | Layout | Responsive spacing. |
| Approved card radius | Layout | Product/collection card surfaces. |
| Approved large radius | Layout | Hero/media/editorial frames. |
| WhatsApp link | Global setting | Shared CTA/contact destination. |
| Section background selector | Section setting | Approved background variants where needed. |
| Section eyebrow | Section setting | Editable section label/kicker. |
| Section heading | Section setting | Editable approved heading. |
| Section body | Section setting | Editable supporting copy. |
| CTA label/link | Section setting | Editable section action. |
| Image picker | Section setting | Merchant-editable imagery. |
| Product picker/list | Section setting | Merchant-editable product references. |
| Collection picker/list | Section/block setting | Merchant-editable collection references. |

## Final Build Inventory

### Existing Files To Modify

- `assets/brand.css`
- `layout/theme.liquid`
- `config/settings_schema.json`
- `config/settings_data.json`
- `sections/hero.liquid`
- `sections/newsletter-cartas.liquid`
- `sections/main-product.liquid`
- `sections/related-products.liquid`
- `snippets/card-product.liquid`
- `assets/section-main-product.css`

### Existing Files To Extend

- `sections/header.liquid`
- `sections/header-group.json`
- `sections/announcement-bar.liquid`
- `sections/footer.liquid`
- `sections/footer-group.json`
- `sections/featured-collection.liquid`
- `sections/collection-list.liquid`
- `sections/main-collection-product-grid.liquid`
- `snippets/card-collection.liquid`
- `snippets/product-media-gallery.liquid`
- `snippets/icon-with-text.liquid`
- `assets/component-card.css`
- `assets/component-price.css`
- `assets/component-product-variant-picker.css`
- `assets/template-collection.css`
- `assets/section-collection-list.css`
- `assets/section-footer.css`
- `assets/component-cart-drawer.css`

### Existing Files To Leave Untouched

- Cart behavior assets
- Product commerce behavior assets
- Search/filter behavior assets
- Customer/localization assets
- Product media behavior assets
- Core commerce snippets
- Utility snippets
- Non-impacted utility/customer/blog/search/cart/page templates

### New Files To Create

- `sections/home-editorial.liquid`
- `sections/product-showcase.liquid`
- `sections/collection-hero-editorial.liquid`
- `sections/collection-support.liquid`
- `sections/product-editorial.liquid`
- `snippets/breadcrumb.liquid`
- `snippets/product-sticky-add.liquid`
- `assets/approved-theme.js` only if needed

### New Metafields To Create

Product:

- `custom.display_category`
- `custom.curation_note`
- `custom.curation_badge`
- `custom.short_spec`
- `custom.material`
- `custom.finish`
- `custom.capacity`
- `custom.contents`
- `custom.care`
- `custom.dimensions`
- `custom.criteria`
- `custom.use_cases`
- `custom.cross_sell_products`

Collection:

- `custom.hero_kicker`
- `custom.hero_subtitle`
- `custom.hero_caption`
- `custom.mafe_note`
- `custom.featured_product`
- `custom.cta_text`
- `custom.cta_link`

Existing retained:

- Product `custom.sell_unit`

### New Metaobjects To Create

- `product_criterion`
- `product_use_case`

### New Theme Settings To Create

- Approved primary font
- Approved label font
- Approved hand font
- Periwinkle
- Periwinkle deep
- Cream canvas
- Coral accent
- Orange accent
- Olive / green deep
- Ink
- Approved max width
- Approved gutter
- Approved card radius
- Approved large radius
- WhatsApp link

## Asset Totals

| Count | Total |
|---|---:|
| Total existing assets reused | 60+ |
| Total existing files modified/refactored | 10 |
| Total existing files extended | 18 |
| Total new theme files required | 7 required, 1 optional |
| Total new product metafields | 13 |
| Total new collection metafields | 7 |
| Total new metaobjects | 2 |
| Total new global theme settings | 15 |

## Final Reference Decision

Implement the approved HostToHost designs as a Shopify-native OS 2.0 architecture over the existing Dawn fork.

The theme remains commerce-first and merchant-editable. Approved design fidelity is achieved by refactoring the global design layer, extending existing Shopify sections/snippets, and adding only a small consolidated set of custom sections where existing assets cannot carry the required layouts cleanly.
