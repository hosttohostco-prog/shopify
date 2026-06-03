# Host-to-Host Shopify Data Model Specification

Version: 1.1 · Status: Approved · Platform: Shopify OS 2.0 · Theme: Dawn 15.4.1

---

## Executive Summary

Canonical data model for Host-to-Host and the source of truth for collection
metafields, product metafields, metaobjects, relationships, merchant workflows,
validation guidance, and governance rules.

This document does **not** define storefront architecture — that remains governed
by `architecture-map.md` and `implementation-plan.md`. Its purpose is to ensure
all future development follows a consistent, scalable, merchant-friendly content
model.

---

## Design Principles

The model must support the approved architecture exactly, stay Shopify-native,
minimise merchant workload, avoid duplicated content, scale with the catalogue,
stay understandable to non-technical users, and support future sections.

**Core rules**

1. Product-specific content belongs on products.
2. Reusable structured content belongs in metaobjects.
3. Use Shopify references, not text relationships.
4. Keep presentation concerns out of stored data.
5. Optimise for merchant usability over theoretical purity.
6. Maintain backwards compatibility wherever possible.

---

## Collection Metafields

Namespace: `custom`. Support the editorial hero, collection narrative, featured
product, and collection CTA. All optional. Unless noted, empty/deleted = hide.

| Key | Type | Length | Purpose | Used By |
|-----|------|--------|---------|---------|
| `hero_kicker` | Single line text | 10–40 chars | Editorial label above title | collection-hero-editorial, collection template |
| `hero_subtitle` | Multi-line text | 50–180 chars | Primary collection introduction | collection-hero-editorial |
| `hero_caption` | Multi-line text | 80–250 chars | Supporting narrative | collection-hero-editorial |
| `mafe_note` | Rich text | 100–500 words | Personal note from Mafe | collection-support |
| `featured_product` | Product reference | — | Editorially featured product | collection-featured-product |
| `cta_text` | Single line text | 20–120 chars | CTA headline | collection-cta |
| `cta_link` | URL | — | CTA destination | collection-cta |

**Notes**
- `featured_product`: if the referenced product is deleted, hide the module;
  merchant should select a replacement.
- `cta_text` / `cta_link`: both drive `collection-cta`; if either is empty the
  CTA section is hidden.

---

## Product Metafields

Namespace: `custom`. All optional. Unless noted, empty/deleted = hide the row or
section.

### Editorial & card fields

| Key | Type | Length | Purpose | Used By |
|-----|------|--------|---------|---------|
| `display_category` | Single line text | 10–40 chars | Editorial category label | product-card, collection-product-grid |
| `curation_note` | Rich text | 50–300 words | Product note from Mafe | product-curation-note |
| `curation_badge` | Single line text | 10–30 chars | Editorial product badge | product-card, product-header |
| `short_spec` | Single line text | 20–80 chars | Compact product summary | product-card |

`display_category`: when empty/deleted, fall back to the collection title.

### Specification rows — `product-specifications-grid`

Each shows a row when populated; hides when empty or deleted.

| Key | Type |
|-----|------|
| `material` | Single line text |
| `finish` | Single line text |
| `capacity` | Single line text |
| `dimensions` | Single line text |
| `contents` | Multi-line text |
| `care` | Multi-line text |

### Reference lists

| Key | Type | Reference | Limit | Purpose | Used By |
|-----|------|-----------|-------|---------|---------|
| `criteria` | List of metaobject refs | `product_criterion` | 4 | Trust criteria section | product-trust-criteria |
| `use_cases` | List of metaobject refs | `product_use_case` | 4 | Use cases section | product-use-cases |
| `cross_sell_products` | List of product refs | — | 4 | Curated related products | product-cross-sell |

**Notes (all three)**: empty = hide section. Deleted references are ignored;
remaining items render. Hide the section if none remain.

---

## Metaobjects

### `product_criterion` — reusable trust criteria

| Field | Type |
|-------|------|
| `title` | Single line text |
| `description` | Multi-line text |
| `icon` | File reference |

Workflow: create once, reuse across products. Behaviour: missing title → don't
render item; missing description → title only; missing icon → default icon;
deleted object → ignored by referencing products.

### `product_use_case` — reusable use cases

| Field | Type |
|-------|------|
| `title` | Single line text |
| `description` | Multi-line text |
| `image` | File reference |

Workflow: create once, reuse across products. Behaviour: missing image → render
without image; missing description → title only; deleted object → ignored by
referencing products.

---

## Relationships

- Collection → Featured Product
- Product → Trust Criteria → Many Products
- Product → Use Cases → Many Products
- Product → Cross-Sell Products

---

## Merchant Workflows

**New collection** — populate: `hero_kicker`, `hero_subtitle`, `hero_caption`,
`mafe_note`, `featured_product`, `cta_text`, `cta_link`. No metaobjects required.

**New product** — populate: `display_category`, `curation_note`,
`curation_badge`, `short_spec`, `material`, `finish`, `capacity`, `dimensions`,
`contents`, `care`. Select: `criteria`, `use_cases`, `cross_sell_products`.

**New trust criterion** — create `product_criterion`; populate `title`,
`description`, `icon`; reuse across products.

**New use case** — create `product_use_case`; populate `title`, `description`,
`image`; reuse across products.

---

## Validation Guidance (recommendations only)

| Field | Recommendation |
|-------|----------------|
| `hero_kicker` | 10–40 chars |
| `hero_subtitle` | 50–180 chars |
| `hero_caption` | 80–250 chars |
| `mafe_note` | 100–500 words |
| `curation_note` | 50–300 words |
| `curation_badge` | 10–30 chars |
| `short_spec` | 20–80 chars |
| `criteria` / `use_cases` / `cross_sell_products` | Max 4 each |

---

## Governance

**Principles** — No new metafields without a reusable business requirement.
Prefer section settings before new metafields; product references over text
handles; metaobject references over numbered fields; reusable content over
duplication. Keep presentation logic out of data, naming consistent, and avoid
one-off fields. Maintain backwards compatibility, deprecate before deleting,
minimise merchant workload, stay Shopify-native.

**Naming** — Namespace `custom`; keys `snake_case`; metaobjects are singular
nouns (`product_criterion`, `product_use_case`).

**Relationships** — Always use Shopify references. Never store handles manually
or duplicate reusable content.

---

## Architecture Review Findings

- Duplicated fields: none.
- Unnecessary complexity: none — architecture remains appropriately lean.
- Missing relationships: none.
- Merchant usability issues: none — workflow remains simple.
- Future maintenance risk: low. If future categories require highly variable
  specifications, review specification modelling in a later architecture phase.
  No action required for Phase 1.

---

## Final Counts

| Item | Count |
|------|-------|
| Collection metafields | 7 |
| Product metafields | 13 |
| **Total metafields** | **20** |
| Metaobjects | 2 |
| Unresolved decisions | None |

**Data model approved and ready for implementation.**
