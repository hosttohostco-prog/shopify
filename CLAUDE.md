# Host To Host — Theme Rules

## Two sources of change

The Shopify Theme Editor and the local repository are both sources of change. Never assume the local repo is the source of truth.

## Before any CLI push

Use the `shopify-theme-sync` skill. It will:

1. Pull high-risk JSON files from the live theme
2. Show the diff
3. Wait for James's review and approval
4. Commit only after approval
5. Push to the intended target theme

## Assume a pull is required if any of the following were recently changed

- Product assignments
- Homepage images or hero content
- Collection images or selections
- CTA links
- Theme settings
- Product page settings

## High-risk files — always pull before pushing

- `templates/index.json` — product assignments, homepage content, CTA links
- `config/settings_data.json` — uploaded images, theme-wide settings
- `templates/product.json` — product page settings
- `sections/header-group.json`
- `sections/footer-group.json`

## Rule

**pull → diff → review → commit → push**

Never commit or push without James's explicit approval. Never push to the live theme unless explicitly requested.

## Store details

- Store: `host-to-host.myshopify.com`
- Live theme: `Host To Host Launch` (ID `157501358279`)
- Default push: targets development theme (`shopify theme push --store host-to-host.myshopify.com`)
- Live push: only when explicitly requested (`--theme 157501358279 --live --allow-live`)
