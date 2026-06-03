# Phase 3B Handoff

## Current status

- Phase 1 complete
- Phase 2 complete
- Phase 3A complete

## Homepage currently

1. Hero
2. Categories
3. Legacy editorial sections
4. Featured collection
5. Selection criteria
6. Favourite product
7. Newsletter

## Target homepage order

1. Hero
2. Categories
3. Por qué existe Host-to-Host
4. Cómo entra un producto
5. La selección de Mafe
6. Notas de anfitriona
7. Lo que viene
8. Newsletter

## Known gaps from Phase 3A

### Hero
- Missing Mafe note card
- Missing scribble

### Categories
- Missing collection imagery
- Missing room kickers
- Missing circular CTA arrows

## Phase 3B objective

**Build:**
- `home-editorial.liquid`
- `product-showcase.liquid`

**Replace:**
- Legacy editorial sections
- Selection criteria section
- Favourite product section

Do not begin Phase 4.

---

## Locked implementation decisions (Phase 3B planning)

These were resolved with James before build and govern the work above.

### Section → module mapping

| Target section | Implementation | Replaces (interim) |
|---|---|---|
| Por qué existe Host-to-Host | `home-editorial.liquid` · **story** variant | `manifesto` |
| Cómo entra un producto | `home-editorial.liquid` · **rules** variant | `editorial_split` |
| La selección de Mafe | `product-showcase.liquid` (Picks) | `favorite_product` |
| Notas de anfitriona | `home-editorial.liquid` · **insights** variant | — (new) |
| Lo que viene | `home-editorial.liquid` · **future** variant | — (new) |

### Decisions

- **`home-editorial.liquid`** is one consolidated section shipping named presets — **story / rules / insights / future** — all content via section settings + blocks. **No metafields, no metaobjects.**
- **Marquee: dropped.** Not in the target order; not built.
- **`product-showcase.liquid` (Mafe's Picks):** bespoke **dark rail**, **per-pick blocks** (product reference + host note + optional category; rank auto from block order), display-only linking to the PDP (no inline quick-add). Pulls real Shopify product data.
- **Featured Collection is removed from the homepage** ("Picks only" — matches the React reference and the target order). The `featured-collection.liquid` section file stays on disk; only its `index.json` instance is removed.
- **`index.json` removals:** `manifesto`, `editorial_split`, `favorite_product`, `featured_collection` instances. All four `.liquid` files remain on disk (non-destructive; Phase 6 cleanup).
- **CSS:** marquee-free; why / selection / picks / insights / future styling centralized in `brand.css` (D4 convention), reusing the `.shead` / `.tlink` utilities from Phase 3A. **No custom JS** (CSS + Dawn's existing scroll animations).

### Out of scope (standing exclusions — future work)

- Room kicker metafields (`custom.hero_kicker`, etc. — Phase 4)
- Collection photography (content/config)
- Circular top-right CTA arrows on category cards
- Hero Mafe note-card and handwritten scribble

### Phase completion checklist (apply at end of 3B)

- [ ] Theme Check passes
- [ ] Shopify preview loads
- [ ] Homepage loads
- [ ] Collection page loads
- [ ] Product page loads

Do not mark Phase 3B complete until all checks pass.
