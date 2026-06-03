# HostToHost Build Status

This is the active implementation tracker for delivering the approved HostToHost Shopify storefront.

## Source of Truth Documents

| Domain | Document |
|----------|----------|
| Architecture | architecture-map.md |
| Implementation | implementation-plan.md |
| Data Model | data-model-specification.md |

Document hierarchy:

1. `architecture-map.md` — what we are building
2. `implementation-plan.md` — in what order we are building it
3. `data-model-specification.md` — how content is stored
4. `build-status.md` — current implementation progress

Do not use this file to revisit architecture decisions. Use it to track implementation status, completion criteria, touched files, progress notes, and outstanding risks.

## Current Status

| Item | Status |
|---|---|
| Active phase | Phase 5 implementation complete → Phase 6 next |
| Overall implementation | In progress |
| Architecture | Complete |
| Delivery plan | Complete |
| Theme base | Existing Dawn 15.4.1 fork retained |
| Core commerce behavior | Preserved |
| Approved design system | Phase 1 global foundation implemented |

## Phase Tracker

| Phase | Objective | Status | Completion Criteria |
|---|---|---|---|
| Phase 1 | Establish approved global design foundation | Complete | Theme uses approved Outfit / Archivo / Shantell Sans system, periwinkle-led palette, approved spacing/radius tokens, and existing commerce behavior remains unchanged. |
| Phase 2 | Align shared commerce components and global chrome | Complete | Header, announcement bar, footer, cart drawer styling, product cards, collection cards, price treatment, and variant presentation match approved system. |
| Phase 3 | Deliver approved homepage | Complete (3A + 3B) | Homepage matches approved React design using Shopify-native sections, consolidated editorial modules, product showcases, collection data, and editable newsletter mechanics. **3A** (refactor existing Hero / Featured Collection / Collection List / Newsletter) complete; **3B** (new `home-editorial` + `product-showcase` consolidated sections — why / rules / picks / insights / future; marquee dropped) complete. Pending Shopify-preview smoke test. |
| Phase 4 | Deliver approved category page | In progress | Category template includes approved editorial hero, compact Mafe note, compact featured product, product grid with header, WhatsApp CTA, and more-category navigation with merchant-editable data. Category strip removed per James 2026-06-03. |
| Phase 5 | Deliver approved product page | Implementation complete (2026-06-03) | PDP hierarchy delivered: hero/buy panel, Mafe Note, Trust Layer (section blocks), accordion, cross-sell rail. Use Cases omitted; Specs Grid replaced by accordion; Sticky Bar deferred. Pending Shopify preview smoke test. |
| Phase 6 | Integrate, QA, merchant-editability validation | Not started | Theme check, storefront smoke test, Theme Editor pass, responsive fidelity, cart/checkout path, collection filtering, PDP purchase path, and dynamic-source fallbacks are verified. |

## Phase 1 — Approved Global Foundation

### Status

Complete (2026-06-02).

### Goal

Move the theme from the earlier Fraunces/DM Sans/DM Mono/Caveat layer to the approved Outfit / Archivo / Shantell Sans, periwinkle-led system without disrupting commerce behavior.

### Completion Criteria

| Criteria | Status |
|---|---|
| Approved font system is loaded and applied globally | Done — `theme.liquid` loads Outfit/Archivo/Shantell Sans; `brand.css` wires `--font-display/-label/-hand` and Dawn's `--font-body-family`/`--font-heading-family` to them |
| Approved periwinkle-led palette is represented in global tokens/settings | Done — approved palette is the canonical token set in `brand.css`; Dawn color schemes in `settings_data.json` migrated to periwinkle-led / cream-canvas / coral-accent |
| Approved spacing, gutter, radius, and section width tokens are in place | Done — `--maxw 1280`, `--gutter clamp(20px,4.5vw,64px)`, `--radius 20`, `--radius-lg 30` from the approved source |
| Existing cart, product form, media, collection, search, and customer behavior remains unchanged | Done — no behavior assets touched; only token/values, font links, and color schemes changed |
| Theme check passes with no new offenses beyond accepted baseline | Done — 2 errors / 13 warnings, all pre-existing; the 2 errors are `main-product.liquid` (Phase 5), and `theme.liquid` carries the same `RemoteAsset` warning the prior font link had |
| Homepage/category/PDP smoke tests pass on desktop and mobile | Pending — requires Shopify preview (no CLI store auth in this env; verify on next push) |

### Files Touched

| File | Action |
|---|---|
| `hosttohost-theme/assets/brand.css` | Major refactor — token layer + type ramp migrated to approved system; legacy token names kept as aliases; Dawn-steering rules preserved |
| `hosttohost-theme/layout/theme.liquid` | Minor refactor — Google Fonts link + noscript swapped Fraunces/DM Sans/DM Mono/Caveat → Outfit/Archivo/Shantell Sans |
| `hosttohost-theme/config/settings_data.json` | Extension — 5 color schemes migrated to periwinkle-led palette (scheme IDs preserved) |
| `hosttohost-theme/config/settings_schema.json` | Extension — added "Brand & contact" group with `whatsapp_link` global setting |

### Progress Notes

- Token migration approach: approved palette/type/layout are the canonical tokens in `brand.css`; the prior `--serif/--sans/--mono/--script`, `--peach/--gold/--blue/--sage/--paper`, and `--hth-*` names are retained as **aliases** mapped onto approved values. This keeps the Dawn-steering CSS and the not-yet-refactored Fraunces-era sections (`hero`, `newsletter-cartas`, `manifesto`, `editorial-split`, `favorite-product`) rendering coherently until Phase 2/3.
- Approved type ramp uses absolute `px` clamps from the source — rem-base-independent, so it reproduces the approved scale exactly on Dawn's 62.5% (1rem=10px) base. Approved utility classes added (`.kicker`, `.display`, `.h-hero/.h-1/.h-2/.h-3`, `.lead`, `.body`, `.hand`, `.tag`, `.tlink`) for Phase 2–5 sections; legacy classes (`.eyebrow`, `.h2-display`, `.h3-display`, `.script`, `.mono`, `.price`) restyled onto the approved system.
- Decision: Dawn's `type_header_font`/`type_body_font` left at `assistant_n4` (vestigial). The theme's established pattern drives fonts via the Google Fonts `<link>` + `brand.css` tokens (full weight/italic control), and `brand.css` overrides the Dawn font vars. Changing the font_picker handles is unnecessary and risk-bearing; not done.
- Stale historical comments referencing Fraunces/DM Sans remain in the lower Dawn-steering block of `brand.css`; those rules are Phase 2 work and will be re-commented when revisited. No runtime effect.

### Outstanding Risks

| Risk | Status |
|---|---|
| Global CSS changes could regress non-target pages | Mitigated — only token values/font links/color schemes changed; no markup or behavior assets. Confirm on Shopify preview smoke test. |
| Dawn rem-base mismatch could distort approved type scale | Resolved — approved ramp uses absolute px clamps (rem-base-independent). |
| Font loading approach must remain performant | Open (low) — Dawn still preloads the vestigial Assistant woff2 alongside the Google Fonts link; minor wasted preload, pre-existing pattern. Revisit if Phase 6 perf needs it. |
| Color contrast needs validation after approved palette is applied | Resolved (James, 2026-06-02) — keep the deepened `--coral-cta #c14d2e` for `.cta`/highest-intent moments (clears WCAG AA with white text). Bright approved `--coral #f39878` stays the non-text accent. |

## Phase 2 — Shared Commerce Components And Chrome

### Status

Complete (2026-06-02).

### Goal

Bring reusable components into the approved design system before building page-specific sections.

### Approach Decisions (approved by James, 2026-06-02)

- **D1** — Replicate the approved hover-reveal quick-add treatment while preserving all existing Dawn quick-add behavior.
- **D2** — Omit wishlist functionality entirely (no backend; out of scope).
- **D3** — Defer host notes, badges, display categories, metafields, and metaobjects to later phases. **No metafield references added in Phase 2.** The existing `custom.sell_unit` reference (prior work, populated) is retained, not added.
- **D4** — Centralize overrides in `brand.css`. Modify Dawn component CSS only with a clear technical reason; stop and request approval before editing additional Dawn component files. (Outcome: **no Dawn component CSS files needed editing** — all alignment achieved in `brand.css`.)
- **D5** — Add the WhatsApp link via `settings.whatsapp_link`; implement the approved dark announcement bar.

### Completion Criteria

| Criteria | Status |
|---|---|
| Header matches approved visual system while preserving menu/search/cart behavior | Done — `brand.css`: translucent cream/blur bar, Archivo-uppercase nav links with periwinkle underline, ink→periwinkle icon hovers, coral cart count. No header markup edits. |
| Announcement bar matches approved system | Done — set to `scheme-5` (ink/cream) in `header-group.json`; Archivo-caps microcopy + coral emphasis + optional WhatsApp CTA (`announcement-bar.liquid` + `brand.css`). Blocks stay editable. |
| Footer matches approved system while preserving menus/brand/social settings | Done — `brand.css`: Archivo-caps muted-cream headings, cream→coral links, Shantell-Sans coral tagline, circular social chips; socials enabled in `footer-group.json`. No footer markup edits. |
| Cart drawer styling aligns without changing cart behavior | Done — Outfit title, approved radius/hairline retained from Phase 1 carryover, refreshed to approved tokens. Behavior untouched. |
| Product card matches approved design and still supports product links, price, sell unit, badges/notes, and quick-add | Done — approved card system in `brand.css`; hover-reveal cream "Agregar" bar overlaid on the media via CSS only (quick-add stays in DOM, all Dawn behavior preserved). |
| Collection card matches approved room/category treatment | Done — `brand.css`: large radius, gradient legibility wash, overlaid cream title/copy, hover lift + zoom. Reusable card only; asymmetric mosaic layout deferred to Phase 3. |
| Price, variant, and media presentation align with approved system | Done — price → Outfit medium (`brand.css`); variant pills/swatches → pill radius + periwinkle selected state. Media gallery untouched (no change required this phase). |

### Files Touched

| File | Action |
|---|---|
| `hosttohost-theme/assets/brand.css` | Primary — reconciled carried-over card/header/footer/cart blocks from Variant-D/Fraunces values to the approved Outfit/Archivo/Shantell + periwinkle reference; added approved announcement-bar, collection-card, price, and variant-picker styling; refreshed stale comments. |
| `hosttohost-theme/sections/announcement-bar.liquid` | Extension — added optional WhatsApp CTA link driven by `settings.whatsapp_link` (inline WA icon + "Pide por WhatsApp"). |
| `hosttohost-theme/sections/header-group.json` | Extension — set announcement-bar `color_scheme` to `scheme-5` (approved dark bar). |
| `hosttohost-theme/sections/footer-group.json` | Extension — enabled `show_social` on the brand block (approved footer socials row). |

Not touched (planned but unnecessary): `header.liquid`, `footer.liquid`, `card-product.liquid`, `card-collection.liquid`, `product-media-gallery.liquid`, and all Dawn component CSS files (`component-card.css`, `component-price.css`, `component-product-variant-picker.css`, `component-cart-drawer.css`, `section-footer.css`, `section-collection-list.css`). All approved alignment was achievable in `brand.css` + section/group config (D4), with no markup changes to the card snippets — the hover-reveal quick-add was solved purely in CSS, preserving all Dawn behavior. `approved-theme.js` was not needed.

### Progress Notes

- The card snippets (`card-product.liquid`, `card-collection.liquid`) required **no markup edits**. The hover-reveal quick-add is achieved by anchoring the existing `.quick-add` over the media bottom (`bottom: calc(100% + gap)` relative to `.card__content--kit`), so the quick-add stays in the DOM and all Dawn behavior (variant modal, bulk, sold-out, product form) is preserved. Revealed on hover/focus for pointer devices; always visible on touch (`@media (hover: hover)`) for accessibility.
- Phase 1 had carried Variant-D/Fraunces-era card/header/footer/cart styling into `brand.css` as aliased placeholders. Phase 2 finished migrating those blocks to the approved system and refreshed the stale Fraunces/DM Sans/DM Mono comments.
- `custom.sell_unit` is the only metafield referenced anywhere in scope; it is **existing, populated** prior work (retained per priority "preserve existing commerce functionality"), not added this phase. No new metafield references were introduced (D3).
- Commerce snippets (`price`, `buy-buttons`, `quantity-input`, variant snippets) remain behaviorally intact; only their shared visual layer was steered.
- Theme check: 171 files, **2 errors / 13 warnings — unchanged from the Phase 1 baseline**; none of the offenses are in Phase 2 files.

### Outstanding Risks

| Risk | Status |
|---|---|
| Product-card refactor can affect multiple templates at once | Mitigated — CSS-only, scoped to existing `.card-wrapper` hooks; no markup or behavior change. Confirm on Shopify preview. |
| Header/footer changes affect all storefront pages | Mitigated — token/cosmetic only; no markup edits. Confirm on Shopify preview. |
| Cart drawer must remain behaviorally unchanged | Resolved — visual tokens only; no behavior assets touched. |
| Hover-reveal quick-add on touch / no-media products | Mitigated — always-visible on touch via `@media (hover: hover)`; no-media products are a rare edge case (curated store carries photos). Confirm on preview. |
| Header translucent backdrop-filter / blur rendering | Open (low) — visual only; confirm on Shopify preview across browsers. |
| Shopify-preview smoke test | Pending — no CLI store auth in this env (same as Phase 1); verify on next push. |

## Phase 3 — Approved Homepage

Phase 3 is delivered in two sub-phases. **3A** refactors the four existing homepage sections; **3B** adds the new consolidated sections. They are tracked separately below.

### Phase 3A — Refactor existing homepage sections

#### Status

Complete (2026-06-02).

#### Scope

Strictly limited to Hero, Featured Collection, Collection List, and Newsletter — refactored in place to match the approved React homepage (`../Home Page` reference). No new sections created. Phase 3B (`home-editorial`, `product-showcase`) explicitly not started.

#### Approach Decisions (approved by James, 2026-06-02)

- **Q1 — index.json composition:** keep the legacy editorial sections (`manifesto`, `editorial_split`, `favorite_product`) in place as **interim fillers** and leave the section order unchanged; only the four in-scope sections' content/settings were updated. Minimal churn; 3B will swap the legacy fillers cleanly.
- **Q2 — Hero:** full approved **photo-led dark-overlay** hero (periwinkle-ink scrim, cream overlaid copy, cream pill primary + ghost-underline secondary). The optional Mafe **stat-card and handwritten scribble were deferred** (lean hero, fewer settings).
- **Q3 — Categories/rooms:** approved **asymmetric mosaic** (first room large, spans 2 rows), gated in Liquid to exactly 3 rooms with a uniform-grid fallback. Room **copy comes from each collection's description**; the per-room **kicker is deferred to Phase 4** (`custom.hero_kicker` metafield) — no collection metafields introduced in 3A.

#### Completion Criteria

| Criteria | Status |
|---|---|
| Homepage section order matches approved flow | Done — order preserved (hero → rooms → [manifesto interim] → featured → [editorial_split interim] → [favorite_product interim] → newsletter); legacy sections are interim until 3B (Q1) |
| Hero matches approved design and remains merchant editable | Done — `hero.liquid` rebuilt as the photo-led overlay stage; all copy/CTAs/scrim/text-color stay section settings |
| Featured Collection matches approved "Piezas para empezar" | Done — added `kicker` + approved `.shead` header with top-right "Ver toda la tienda" `.tlink`; product grid still renders the Phase-2 `card-product` (quick-add untouched) |
| Categories/rooms use Shopify collection data | Done — `collection-list.liquid` mosaic layout over existing `card-collection`; copy from `collection.description` |
| Newsletter form remains Shopify-native and editable | Done — Dawn `{% form 'customer' %}` mechanics unchanged; restyled to the approved Signup card + optional WhatsApp button (`settings.whatsapp_link`) |
| Mobile and desktop homepage match approved design | Pending — requires Shopify preview smoke test |
| Theme check holds the accepted baseline | Done — 171 files, **2 errors / 13 warnings, unchanged**; none of the offenses are in Phase 3A files (errors are pre-existing `featured-product.liquid` / `main-product.liquid`) |

#### Files Touched

| File | Action |
|---|---|
| `hosttohost-theme/sections/hero.liquid` | Major refactor — rebuilt into the approved photo-led overlay stage on approved tokens; stat-card/scribble deferred (Q2). Defaults flipped to dark scrim / light text. |
| `hosttohost-theme/sections/newsletter-cartas.liquid` | Major refactor — approved Signup card (white panel, periwinkle deco, pill field, uppercase note, "o" divider, optional WhatsApp button). Form internals untouched; added `body`, `show_whatsapp`, `whatsapp_label` settings. |
| `hosttohost-theme/sections/featured-collection.liquid` | Extension — added `kicker` + `header_link_label`/`header_link_url` settings and an approved `.shead` header (markup-minimal; no commerce/grid logic changed). |
| `hosttohost-theme/sections/collection-list.liquid` | Extension — added `kicker`, `header_link_*`, and a `layout` (mosaic/uniform) setting; mosaic gated to 3 blocks via `collection-list--mosaic`. No block-model change. |
| `hosttohost-theme/assets/brand.css` | Extension — added shared `.shead` / `.shead-l` / `.shead-sub` / `.tlink` header utilities and the `collection-list--mosaic` grid mapping (D4: Dawn-section overrides centralized here). |
| `hosttohost-theme/templates/index.json` | Composition update — approved copy/settings for the four in-scope sections; legacy sections + order left intact (Q1). |

Not touched: `card-product.liquid`, `card-collection.liquid` (already carry the approved markup hooks; Phase 2 styled them), all Dawn component CSS, and all commerce JS. No `home-editorial`/`product-showcase`. No metafields/metaobjects introduced.

#### Progress Notes

- The room **kicker** the approved design shows per card has no field in `card-collection` and collection metafields are out of 3A scope, so it is deferred to Phase 4 (`custom.hero_kicker`). 3A rooms therefore render title + description-derived copy only.
- Mosaic is desktop-only (≥990px) and Liquid-gated to exactly 3 rooms; any other count falls back to Dawn's uniform grid, keeping the section scalable and merchant-safe.
- Featured/Collection-list headers reuse new shared `.shead`/`.tlink` utilities (also intended for 3B's Picks/Insights/Future), avoiding per-section header CSS.
- Hero placeholder is now a periwinkle-led gradient (matching the approved dark stage) until Mafe uploads a hero photo; scrim/text-color toggles still support a future bright photo.

#### Outstanding Risks

| Risk | Status |
|---|---|
| `em-coral` accent over the dark hero scrim must clear contrast | Mitigated — accent word uses coral on the periwinkle-ink scrim; confirm against the AA decision on preview |
| Mosaic fixed-height cells vs Dawn's ratio cards | Mitigated — mosaic overrides the ratio box to stretch cards to the grid cell at ≥990px only; mobile keeps Dawn ratio cards |
| Featured header link vs Dawn's bottom view-all button | Mitigated — `show_view_all` set false in index.json; header `.tlink` carries "ver toda la tienda" |
| Shopify-preview smoke test | Pending — verify hero overlay, rooms mosaic, newsletter card, and WhatsApp button on next push |

### Phase 3B — New consolidated homepage sections

#### Status

Complete (2026-06-03).

#### Scope

`home-editorial.liquid` (one consolidated section, four approved presets) and `product-showcase.liquid` (Mafe's picks dark rail), replacing the interim `manifesto` / `editorial_split` / `favorite_product` and removing the `featured_collection` instance from the homepage. Screenshots were the source-of-truth visual reference; React `styles.css`/`sections.jsx` the implementation spec.

#### Section → module mapping (as built)

| Target module | Implementation | Replaces |
|---|---|---|
| Por qué existe Host-to-Host | `home-editorial` · **story** preset | `manifesto` |
| Cómo entra un producto | `home-editorial` · **rules** preset (repeatable `rule` blocks) | `editorial_split` |
| La selección de Mafe | `product-showcase` (dark rail, `pick` blocks) | `favorite_product` |
| Notas de anfitriona | `home-editorial` · **insights** preset (repeatable `insight` blocks) | — (new) |
| Lo que viene | `home-editorial` · **future** preset (repeatable `future` blocks) | — (new) |

#### Decisions honored

- One consolidated `home-editorial.liquid` shipping named presets (story / rules / insights / future); a `variant` select drives layout; content via settings + blocks. **No metafields, no metaobjects.**
- **Marquee dropped** (not in target order; not built).
- `product-showcase` is **display-only**: per-pick **product reference** pulls real Shopify data (image, title, price, PDP link); rank auto from block order; optional category override + host note. **No quick-add / cart / commerce logic.**
- **`featured_collection` instance removed** from `index.json` ("Picks only" — matches React `app.jsx`, which does not mount Featured). All four legacy `.liquid` files remain on disk (Phase 6 cleanup).
- **All styling centralized in `brand.css`** (D4 + James's 3B instruction); no section `{% stylesheet %}` blocks, no new CSS files. Reuses `.kicker`/`.display`/`.lead`/`.shead`/`.tlink`/`.tag` utilities; ports `.btn`/`.btn--coral`, `.tag .dot`, `.card-watermark` once. Reveal = Dawn's native `scroll-trigger animate--slide-in`. **No custom JS.**
- Accent words use the inline_richtext `<em>` tag, recolored per variant (coral-deep on rules/picks, periwinkle-deep on insights); the story accent gets the hand-drawn coral scribble (ported Scribble SVG).

#### Completion Criteria

| Criteria | Status |
|---|---|
| Homepage order matches approved flow (hero → categories → story → rules → picks → insights → future → newsletter) | Done — `index.json` recomposed; legacy + featured instances removed |
| `home-editorial.liquid` created with story/rules/insights/future presets | Done |
| `product-showcase.liquid` created (real product refs, display-only, no quick-add) | Done |
| Approved content is Shopify-native editable, not hardcoded | Done — all copy/imagery/links via section settings + blocks |
| No metafields / metaobjects introduced | Done |
| Styling centralized in `brand.css`; no new CSS files | Done |
| Theme check holds the accepted baseline | Done — 173 files, **2 errors / 13 warnings**, unchanged baseline; no offenses in 3B files |
| Mobile/desktop homepage matches approved design | Pending — requires Shopify preview smoke test |

#### Files Touched

| File | Action |
|---|---|
| `hosttohost-theme/sections/home-editorial.liquid` | New — consolidated editorial section (4 presets, `rule`/`insight`/`future` blocks) |
| `hosttohost-theme/sections/product-showcase.liquid` | New — Mafe's Picks dark rail (`pick` product-reference blocks, display-only) |
| `hosttohost-theme/assets/brand.css` | Extension — Phase 3B module CSS (faithful port of approved `.why`/`.selection`/`.picks`/`.ins-grid`/`.fut-grid` + shared `.btn`/`.btn--coral`/`.tag .dot`/`.card-watermark`) |
| `hosttohost-theme/templates/index.json` | Composition update — approved order; removed `manifesto` / `editorial_split` / `favorite_product` / `featured_collection` instances |

Not touched: legacy `.liquid` files (`manifesto`, `editorial-split`, `favorite-product`, `featured-collection`) remain on disk, unused on the homepage (Phase 6 cleanup). No Dawn component CSS or commerce JS. No `card-product`/`card-collection` markup edits.

#### Known Gaps / Notes

- **Picks need product assignment:** the four `pick` blocks ship with host note + tint but no product selected; Mafe assigns products in the Theme Editor. Empty picks render a graceful "Selecciona un producto" placeholder; products without a photo fall back to a tinted card with a faded title watermark + "Foto" label.
- **Story image** is a sage placeholder until a portrait is uploaded (`image_picker`).
- **Out of scope (unchanged):** hero stat-card/scribble, room kickers/photography/CTA arrows (Phase 4 data), all metafields/metaobjects.

#### Outstanding Risks

| Risk | Status |
|---|---|
| Consolidated sections could become confusing if too many layout modes are exposed | Mitigated — single `variant` select + clearly-labeled per-variant settings/blocks; presets seed the correct block set |
| Approved React content must be Shopify-native editable, not hardcoded | Resolved — all content via settings/blocks; real product data via references |
| Product showcase must reuse product behavior correctly | Resolved — display-only by design (no cart/quick-add); links to PDP only, no commerce logic touched |
| Shopify-preview smoke test | Pending — verify story/rules/picks/insights/future render + responsive on next push |

## Phase 4 — Approved Category Page

### Status

In progress (2026-06-03). Implementation complete; pending Shopify preview smoke test.

### Scope Decision (2026-06-03)

Category navigation pill strip removed entirely. Revised page structure:
Breadcrumb → Hero → Compact Mafe Note → Compact Featured Product → Product Grid → WhatsApp CTA → More Categories.

### Goal

Turn `collection.json` from a generic Dawn collection template into the approved category experience while preserving filtering, sorting, pagination, and product-card reuse.

### Completion Criteria

| Criteria | Status |
|---|---|
| Breadcrumb is present and reusable for Phase 5 | Done — `snippets/breadcrumb.liquid` created |
| Category hero is collection-driven and matches approved design | Done — `sections/collection-hero-editorial.liquid` created |
| Compact Mafe Note uses collection metafield with block-setting fallback | Done — `note` block in `collection-support.liquid` |
| Compact Featured Product is collection-driven with product reference | Done — `featured` block in `collection-support.liquid` |
| Product grid keeps Shopify filters, sorting, pagination, and product-card reuse | Done — Dawn commerce layer untouched; grid header added above paginate |
| Grid has approved editorial header (kicker + title + view-all link) | Done — new settings added to `main-collection-product-grid.liquid` |
| WhatsApp CTA section is editable and responsive | Done — `cta` block in second `collection-support` instance |
| More Categories section uses `collection-list` with uniform 2-col layout | Done — second `collection-list` instance in `collection.json` |
| Template composition matches approved order | Done — `collection.json` recomposed |
| All collection metafields have graceful fallbacks when empty | Done — each block hides when both metafield and block fallback are blank |
| Mobile layouts are handled (hero, note, featured) | Done — responsive breakpoints in `brand.css` |

### Files Touched

| File | Action |
|---|---|
| `hosttohost-theme/templates/collection.json` | Full recompose — 5 sections replacing vanilla Dawn 2-section template |
| `hosttohost-theme/sections/main-collection-product-grid.liquid` | Extension — grid header (kicker + title + link) added above `{%- paginate -%}`; 4 new settings added to schema |
| `hosttohost-theme/assets/brand.css` | Extension — Phase 4 CSS block (breadcrumb, hero, note, featured, grid header, CTA, outline button) |

New files:

| File | Type |
|---|---|
| `hosttohost-theme/sections/collection-hero-editorial.liquid` | Section — editorial hero with breadcrumb, scrim, metafield-driven copy |
| `hosttohost-theme/sections/collection-support.liquid` | Section — multi-block (note / featured / cta); two instances in collection.json |
| `hosttohost-theme/snippets/breadcrumb.liquid` | Snippet — reusable Archivo-caps breadcrumb (collection + product contexts) |

### Metafields

| Metafield | Status | Used By |
|---|---|---|
| `custom.hero_kicker` | Pending Shopify admin definition | `collection-hero-editorial` kicker |
| `custom.hero_subtitle` | Pending Shopify admin definition | `collection-hero-editorial` subtitle |
| `custom.hero_caption` | Pending Shopify admin definition | `collection-hero-editorial` caption badge |
| `custom.mafe_note` | Pending Shopify admin definition | `collection-support` note block |
| `custom.featured_product` | Pending Shopify admin definition | `collection-support` featured block |
| `custom.cta_text` | Pending Shopify admin definition | `collection-support` cta block |
| `custom.cta_link` | Pending Shopify admin definition | `collection-support` cta block |

All 7 collection metafields must be created in Shopify Admin → Content → Metafields → Collections before content can be populated. The theme gracefully hides empty sections until populated.

### Progress Notes

- Category strip (nav pills) removed per James 2026-06-03.
- `collection-support.liquid` is used twice in the template: once for note + featured (before the grid), once for the CTA (after the grid).
- `main-collection-product-grid` grid header uses the existing `.shead` + `.kicker` + `.tlink` + `.display` utilities from Phase 3A. The header is outside `{%- paginate -%}` — zero risk to commerce behavior.
- `collection-list` reused for "More Categories" with `layout: uniform, columns_desktop: 2` (matches the approved 2-col room card layout). The same Phase 3A kicker support is available per block.
- `main-collection-banner` section is still in the theme directory but is no longer referenced by `collection.json`. It remains for Phase 6 cleanup.
- `assets/template-collection.css` was not modified — all Phase 4 overrides are in `brand.css` per D4 convention.

### Outstanding Risks

| Risk | Status |
|---|---|
| Empty collection metafields must produce graceful fallbacks | Resolved — `{%- if ... != blank -%}` guards on every block |
| Product-grid filter/sort behavior must not regress | Resolved — header added before `{%- paginate -%}`, zero touch to Dawn commerce machinery |
| Shopify preview smoke test | Pending — verify all 5 sections, metafield fallbacks, filter/sort, mobile layouts on next push |
| `collection-list` in collection.json needs blocks added in Theme Editor | Open — "More Categories" section will render empty until room collection blocks are added |

## Phase 5 — Approved Product Page

### Status

Implementation complete (2026-06-03). Pending Shopify preview smoke test.

### Goal

Deliver the approved PDP hierarchy while preserving Dawn product form, variant, media, price, cart, and related-product behavior.

### Approved Page Hierarchy (as built)

```
1. Hero (main-product section)
   ├─ Breadcrumb
   ├─ Gallery (thumbnail layout, "Selección de Mafe" badge top-left)
   └─ Buy panel
       ├─ Category kicker (custom.display_category → vendor fallback)
       ├─ Product title (h1)
       ├─ Subtitle / spec line (descriptors.subtitle)
       ├─ Price
       ├─ Variant selector (circle swatches)
       ├─ Quantity stepper
       ├─ Add-to-bag CTA
       ├─ Assurance row (✓ Entrega 24h · ✓ Cambios 30 días)
       └─ WhatsApp link

2. Mafe Note (product-editorial.liquid)
   └─ Shantell Sans coral-deep quote from custom.curation_note; periwinkle avatar

3. Trust Layer (product-trust.liquid — section blocks)
   ├─ Hardcoded kicker: "Por qué entraron a la colección"
   ├─ Hardcoded H2: "No es un producto que encontré. Es uno que ya repongo."
   └─ Up to 4 criterion blocks (heading + body, pre-seeded with approved copy)

4. Product Information (main-product accordion blocks — retained)
   ├─ Materials
   ├─ Shipping & Returns
   ├─ Dimensions
   └─ Care Instructions

5. Cross-sell rail (related-products.liquid — refactored)
   ├─ Kicker: "Explora el resto de la colección"
   ├─ H2: "Más piezas que ya probé"
   ├─ "Ver toda la tienda →" link
   └─ Horizontal scroll rail (cs-card snippet, custom.cross_sell_products → recommendation API fallback)
```

### Approved Implementation Decisions

- **A1 — Mafe Note placement:** moved out of the buy panel into a standalone `product-editorial` section immediately below the hero. Reduces above-the-fold density; note still intercepts the customer before the Trust Layer.
- **A2 — Accordion retained, Specs Grid omitted:** the four Dawn `collapsible_tab` blocks are kept and restyled in `brand.css`. No specs grid metafield. Content is merchant-editable text in the theme editor.
- **A3 — Use Cases omitted:** intentionally excluded after PDP hierarchy review. `product_use_case` metaobject not created. `custom.use_cases` metafield not created.
- **A4 — Trust Layer via section blocks, not metaobjects:** criteria implemented as `criterion` blocks (heading + body) within `product-trust.liquid`. `product_criterion` metaobject and `custom.criteria` metafield not created. All four criteria pre-seeded with approved copy in `product.json`.
- **A5 — Sticky Bar deferred:** `snippets/product-sticky-add.liquid` not built. Deferred as an optional enhancement for a later phase.
- **A6 — No `section-main-product.css` edits:** all visual overrides go in `brand.css` (D4 convention).
- **A7 — Title `<em>` accent not implemented:** plain `product.title` used. The accent word in the approved React design is not reproducible through standard Shopify product data. Accepted constraint.
- **A8 — Cross-sell data source:** `custom.cross_sell_products` metafield is primary; Shopify recommendations API is the fallback.

### Completion Criteria

| Criteria | Status |
|---|---|
| Breadcrumb renders above the gallery/buy panel grid | Done |
| Gallery uses thumbnail strip layout; "Selección de Mafe" badge overlaid top-left | Done |
| Buy panel contains kicker, title, subtitle, price, variants, qty, CTA, assurances, WhatsApp | Done |
| Product form, variants, quantity, and add-to-cart remain fully functional | Pending preview smoke test |
| Dynamic checkout row disabled | Done |
| Mafe Note renders as standalone full-width block below the hero | Done |
| Trust Layer renders below Mafe Note with sage-soft background and 4 pre-seeded criterion blocks | Done |
| Product Information accordion (4 blocks) retained and positioned below Trust Layer | Done |
| Cross-sell rail renders as horizontal scroll with nav arrows and host-quote card overlay | Done |
| Template order: main → product-editorial → product-trust → related-products | Done |
| Mobile: gallery stacks above buy panel | Pending preview smoke test |

### Files Touched

| File | Action |
|---|---|
| `hosttohost-theme/sections/main-product.liquid` | Targeted additions — breadcrumb; gallery badge; `display_category`, `assurance`, `whatsapp_link` block cases + schema |
| `hosttohost-theme/sections/product-editorial.liquid` | New — Mafe Note section (reads `custom.curation_note`; renders nothing if blank) |
| `hosttohost-theme/sections/product-trust.liquid` | New — Trust Layer section (up to 4 `criterion` blocks; sage-soft bg; hardcoded heading/eyebrow) |
| `hosttohost-theme/sections/related-products.liquid` | Refactored — horizontal scroll rail with `cs-card` snippet, nav arrows, `custom.cross_sell_products` → recommendation API fallback |
| `hosttohost-theme/snippets/cs-card.liquid` | New — cross-sell card with tonal background, host-quote overlay, price |
| `hosttohost-theme/templates/product.json` | Full recompose — approved block order, 4 criterion blocks pre-seeded, section order updated |
| `hosttohost-theme/assets/brand.css` | Phase 5 CSS — hero grid, gallery badge, buy panel, Mafe Note, Trust Layer, accordion restyle, cross-sell rail |

Not changed: `assets/section-main-product.css` (D4), all Dawn component CSS, all commerce JS.

### Metafields

| Metafield | Type | Status | Used By |
|---|---|---|---|
| `custom.display_category` | Single line text | Pending admin definition + population | Buy panel kicker |
| `custom.curation_note` | Multi-line text | Pending admin definition + population | Mafe Note section |
| `custom.curation_badge` | Single line text | Pending admin definition | Gallery badge override (optional; section defaults to "Selección de Mafe") |
| `custom.cross_sell_products` | List of product references | Pending admin definition + population | Cross-sell rail primary source |
| `custom.sell_unit` | Single line text | Existing, populated | Pack unit label |

Metafields removed from Phase 5 scope:

| Metafield | Decision |
|---|---|
| `custom.short_spec` | Not needed — subtitle comes from `descriptors.subtitle` |
| `custom.material` / `finish` / `capacity` / `contents` / `care` / `dimensions` | Not needed — content lives in accordion blocks |
| `custom.criteria` | Not needed — Trust Layer uses section blocks |
| `custom.use_cases` | Not needed — Use Cases section omitted |
| `custom.cross_sell_products` | Pending admin setup (see above) |

### Metaobjects

Neither `product_criterion` nor `product_use_case` was created. Trust Layer uses section blocks; Use Cases was omitted.

### Outstanding Risks

| Risk | Status |
|---|---|
| Product form, variant state, and add-to-cart after `main-product` changes | Open — verify on Shopify preview |
| Gallery thumbnail layout on mobile | Open — verify on Shopify preview |
| Cross-sell rail empty state before `custom.cross_sell_products` is populated | Mitigated — recommendation API fallback is active |
| Accordion content is currently empty — needs merchant population in theme editor | Open (content task) |

## Phase 6 — Integration And Release Validation

### Status

Not started.

### Goal

Validate the approved storefront as a coherent Shopify-native build and confirm merchant editability across homepage, category, and PDP.

### Completion Criteria

| Criteria | Status |
|---|---|
| Theme check completed with no new unresolved issues | Pending |
| Homepage, category pages, and product pages match approved designs on desktop and mobile | Pending |
| Theme Editor editability verified for all new/consolidated sections | Pending |
| Product add-to-cart, cart drawer, and checkout path verified | Pending |
| Collection filtering, sorting, and pagination verified | Pending |
| Dynamic-source and empty-data fallbacks verified | Pending |
| Final Shopify preview reviewed | Pending |

### Files Touched

None yet.

Expected files:

| File | Expected Action |
|---|---|
| Template JSON defaults | Final configuration adjustments only |
| `hosttohost-theme/config/settings_data.json` | Final default/settings adjustments only |

### Progress Notes

- This phase should not introduce new architecture.
- Any issues found here should be treated as polish, data, or regression fixes.

### Outstanding Risks

| Risk | Status |
|---|---|
| Cross-page visual inconsistency | Open |
| Missing data causing blank sections | Open |
| Merchant settings becoming confusing | Open |
| Performance issues from image-heavy approved sections | Open |

## Files Touched Log

Track actual implementation edits here as phases progress.

| Date | Phase | Files Touched | Notes |
|---|---|---|---|
| 2026-06-02 | Setup | `build-status.md` | Created active implementation tracker based on `docs/implementation-plan.md`. |
| 2026-06-02 | Phase 1 | `assets/brand.css`, `layout/theme.liquid`, `config/settings_data.json`, `config/settings_schema.json` | Migrated global foundation to approved Outfit/Archivo/Shantell Sans + periwinkle-led system. Legacy tokens kept as aliases; commerce behavior untouched. Theme check: no new offenses. |
| 2026-06-02 | Phase 2 | `assets/brand.css`, `sections/announcement-bar.liquid`, `sections/header-group.json`, `sections/footer-group.json` | Aligned shared chrome + commerce components (header, announcement bar, footer, cart drawer, product/collection cards, price, variant) to the approved reference. All CSS centralized in `brand.css` (D4); hover-reveal quick-add solved in CSS with zero card-snippet markup edits, preserving all Dawn behavior. No metafield references added (D3). No Dawn component CSS files touched. Theme check: 2 errors / 13 warnings, unchanged baseline. |
| 2026-06-03 | Phase 3B (Picks fix) | `sections/product-showcase.liquid`, `templates/index.json` | Picks now ALWAYS render the text layer (category / title / host quote / price) beneath each card. Previously the body rendered only when a product reference was assigned, so the seeded (product-less) picks showed media placeholders with no text. Added gated placeholder fields to the `pick` block (`title`, `price`; `category` reused) that the real product reference overrides once assigned — title→`product.title`, category→`product.type`, price→`product.price | money`. Seeded the four reference picks (Copas de vino / Utensilios BBQ / Velas aromáticas / Toallas desmaquillantes) with category + quote + price. Placeholder tint backgrounds retained. Theme check unchanged. |
| 2026-06-03 | Phase 4 | `sections/collection-hero-editorial.liquid` (new), `sections/collection-support.liquid` (new), `snippets/breadcrumb.liquid` (new), `sections/main-collection-product-grid.liquid`, `assets/brand.css`, `templates/collection.json` | Delivered approved category page: editorial hero (collection image + scrim + metafield-driven copy), compact Mafe Note, compact Featured Product, product grid with shead header, WhatsApp CTA, more-categories. Category strip removed per James. All CSS in brand.css (D4). `collection.json` recomposed from vanilla 2-section Dawn to 5-section approved order. Grid header added before paginate (zero commerce regression risk). Metafield definitions pending in Shopify admin. Theme check pending next push. |
| 2026-06-03 | Phase 3B (Categories refinement) | `snippets/card-collection.liquid`, `sections/collection-list.liquid`, `assets/brand.css`, `templates/index.json` | Refined the Categories (rooms) mosaic to the approved reference per James's 8-point audit: widened the section toward the hero (`.section-collection-list` page-width → `min(1640px, 100%)`), tightened heading→cards spacing (section padding 64/72 → 40/56; `.shead` margin reduced), shortened the mosaic (`height` clamp 540–700 → 440–600), enforced large poster radius (clip `.card` to `--radius-lg`), added per-card editorial kickers + a top-right circular outlined arrow, and moved card content up with breathing room. Kickers are a new **section block setting** (`featured_collection.kicker`) — **not** the Phase 4 `custom.hero_kicker` metafield (content model untouched). `card-collection.liquid` got an additive, gated `card_kicker` param (kicker in both content blocks + room caption kept visible on image cards + arrow); empty/omitted elsewhere = no change to other card usages. This is the first edit to `card-collection.liquid` (Phase 2/3A had left it untouched), now justified by James's explicit request. Theme check unchanged (2 errors / 13 warnings; none in touched files). |
| 2026-06-03 | Phase 3B | `sections/home-editorial.liquid` (new), `sections/product-showcase.liquid` (new), `assets/brand.css`, `templates/index.json` | Built the two consolidated homepage sections and recomposed the homepage to the approved order. `home-editorial` ships story/rules/insights/future presets (variant select + `rule`/`insight`/`future` blocks); `product-showcase` is a display-only dark Picks rail (per-pick product reference, auto rank, no quick-add). Removed `manifesto`/`editorial_split`/`favorite_product`/`featured_collection` instances from `index.json` (files stay on disk). All CSS in `brand.css` (faithful port of approved modules; reused existing utilities; no new files, no section stylesheet blocks, no JS, no metafields/metaobjects). Theme check: 173 files, 2 errors / 13 warnings — unchanged baseline; no offenses in 3B files. |
| 2026-06-03 | Phase 5 | `sections/main-product.liquid`, `sections/product-editorial.liquid` (new), `sections/product-trust.liquid` (new), `sections/related-products.liquid`, `snippets/cs-card.liquid` (new), `templates/product.json`, `assets/brand.css` | Delivered approved PDP hierarchy. Hero/buy panel: breadcrumb, gallery badge, display_category kicker, assurance row, WhatsApp link. Mafe Note: standalone product-editorial section reading custom.curation_note. Trust Layer: new product-trust section with up to 4 criterion blocks (section blocks, not metaobjects); sage-soft bg; heading/eyebrow hardcoded; all 4 criteria pre-seeded in product.json. Accordion: existing Dawn collapsible_tab blocks retained in place of a specs grid. Cross-sell rail: related-products refactored to horizontal scroll with cs-card snippet, nav arrows, custom.cross_sell_products → recommendation API fallback. Approved decisions: Use Cases omitted; Specs Grid replaced by accordion; Sticky Bar deferred; product_criterion metaobject not created; all CSS in brand.css (D4). |
| 2026-06-02 | Phase 3A | `sections/hero.liquid`, `sections/newsletter-cartas.liquid`, `sections/featured-collection.liquid`, `sections/collection-list.liquid`, `assets/brand.css`, `templates/index.json` | Refactored the four existing homepage sections to the approved design. Hero → photo-led dark overlay (stat-card/scribble deferred, Q2). Newsletter → approved Signup card + WhatsApp; Dawn form internals untouched. Featured + Collection List → added kicker/header-link/`.shead`; collection-list got a 3-room mosaic layout (Q3, copy from collection.description; kicker deferred to Phase 4). Shared `.shead`/`.tlink`/mosaic CSS added to `brand.css` (D4). index.json: approved copy/settings; legacy editorial sections + order kept as interim (Q1). No metafields/metaobjects, no new sections, no Dawn component CSS/JS. Theme check: 2 errors / 13 warnings, unchanged baseline; no offenses in 3A files. |

## Progress Notes Log

| Date | Phase | Note |
|---|---|---|
| 2026-06-02 | Setup | Architecture phase is complete. Implementation tracker created. Active implementation has not started. |
| 2026-06-02 | Phase 1 | Approved global design foundation implemented across the 4 scope files. No architectural conflicts found. Brand palette/type/spacing held as fixed brand tokens in `brand.css` (brand consistency); only the shared WhatsApp link added as a new merchant-editable global setting. CTA-contrast resolved: keep deepened `--coral-cta #c14d2e` (AA). Remaining open item: Shopify-preview smoke test (no store auth in this env). Phase 2 (shared commerce components + chrome) is the next phase — **on hold pending James's go-ahead.** |
| 2026-06-02 | Phase 2 | Shared components + chrome aligned to the approved reference. Scope landed in 4 files, all within the brand-steering layer + section config — **no Dawn component CSS files were needed** (D4 satisfied; none required a clear technical reason to edit) and **no card-snippet markup edits** were needed (the hover-reveal quick-add was solved purely in CSS, preserving every Dawn quick-add path). Key realization: Phase 1 had carried half-migrated Variant-D/Fraunces card/header/footer/cart styling into `brand.css`; Phase 2 completed that migration and added the missing announcement/collection-card/price/variant approved styling. No metafield references introduced (D3); existing populated `custom.sell_unit` retained. Theme check unchanged (2 errors / 13 warnings, all pre-existing, none in Phase 2 files). Open item: Shopify-preview smoke test (no store auth in this env). Phase 3 (approved homepage) is next — **on hold pending James's go-ahead.** |
| 2026-06-03 | Phase 3B | Built `home-editorial.liquid` (story/rules/insights/future presets) + `product-showcase.liquid` (Mafe's Picks, display-only product-reference rail) and recomposed `index.json` to the approved order (hero → categories → story → rules → picks → insights → future → newsletter). Removed the interim `manifesto`/`editorial_split`/`favorite_product` and the `featured_collection` homepage instance ("Picks only", matching React `app.jsx`); all four `.liquid` files remain on disk for Phase 6 cleanup. **No metafields/metaobjects, no new CSS files, no section stylesheet blocks, no custom JS** — all styling ported into `brand.css` (D4 + James's 3B instruction), reusing the `.kicker`/`.display`/`.lead`/`.shead`/`.tlink`/`.tag` utilities and adding shared `.btn`/`.btn--coral`/`.tag .dot`/`.card-watermark`. Accent words via `<em>` recolored per variant; story scribble ported from the React Scribble SVG. Theme check held the baseline (173 files, 2 errors / 13 warnings; none in 3B files). Open items: **assign products to the four Picks** in the Theme Editor; upload the story portrait; Shopify-preview smoke test. **Phase 3 complete — Phase 4 (category page) is next, on hold pending James's go-ahead.** Phase 4 will introduce the collection metafields previewed at the 3A checkpoint. |
| 2026-06-02 | Phase 3A | Refactored the four in-scope homepage sections (Hero, Featured Collection, Collection List, Newsletter) to the approved React homepage; **no new sections, no metafields/metaobjects, no Dawn component CSS or JS touched.** Decisions: Q1 keep legacy editorial sections + order as interim until 3B; Q2 full photo-led hero with stat-card/scribble deferred; Q3 3-room mosaic with copy from collection.description and the per-room kicker deferred to Phase 4 (`custom.hero_kicker`). Shared `.shead`/`.tlink` header utilities + the `collection-list--mosaic` grid added to `brand.css` (D4), intended for reuse by 3B's Picks/Insights/Future. Theme check held the baseline (2 errors / 13 warnings, none in 3A files; CLI was available this run). Open item: Shopify-preview smoke test (verify hero overlay, rooms mosaic, newsletter card, WhatsApp button). **Phase 3B (`home-editorial` + `product-showcase`) is next — on hold pending James's go-ahead.** Phase 4 collection metafield schema previewed at the 3A checkpoint (not implemented): `custom.hero_kicker`/`hero_subtitle`/`hero_caption` (single_line_text), `mafe_note` (multi_line_text), `featured_product` (product_reference), `cta_text` (single_line_text), `cta_link` (url). |

## Outstanding Risks Summary

| Risk | Severity | Phase | Status |
|---|---|---|---|
| Global CSS refactor can regress broad theme surfaces | High | Phase 1 | Mitigated — token-only change, no behavior assets; pending preview smoke test |
| Product-card refactor affects many templates | High | Phase 2 | Mitigated — CSS-only, no markup/behavior change; pending preview smoke test |
| Consolidated sections may become too complex for merchants | Medium | Phase 3 | Mitigated — `home-editorial` uses a single `variant` select with clearly-labeled per-variant settings/blocks; presets seed the right blocks |
| Collection metafield fallbacks must be robust | Medium | Phase 4 | Open |
| `main-product` refactor can break product form/variant/media behavior | High | Phase 5 | Open |
| Product metaobject/data population may take longer than theme work | Medium | Phase 5 | Open |
| Final visual fidelity depends on product/category imagery quality | Medium | Phase 6 | Open |

## Definition Of Done

The approved HostToHost storefront is complete when:

- The current Dawn commerce foundation remains intact.
- Approved homepage, category page, and product page designs are implemented.
- All new approved sections are merchant-editable through Shopify OS 2.0.
- Product and collection content uses dynamic sources, metafields, or metaobjects where required.
- No approved content is unnecessarily hardcoded.
- Theme check and storefront purchase-path testing pass.
- Desktop and mobile views match the approved React designs within Shopify implementation constraints.
