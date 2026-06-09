# Host To Host — Theme Rules

## Two sources of change

The Shopify Theme Editor and the local repository are both sources of change. Never assume the local repo is the source of truth.

## Before any CLI push

Use the `shopify-theme-sync` skill. It will:

1. Pull high-risk JSON files from the live theme
2. Show the diff
3. Warn before overwriting any Theme Editor changes
4. Commit the pulled state before pushing

## High-risk files — always pull before pushing

- `templates/index.json` — product assignments, homepage content, CTA links
- `config/settings_data.json` — uploaded images, theme-wide settings
- `sections/header-group.json`
- `sections/footer-group.json`

## Rule

**pull → diff → warn → commit → push**

Never overwrite `templates/index.json` or `config/settings_data.json` without checking for live changes first.

## Store details

- Store: `host-to-host.myshopify.com`
- Live theme: `Host To Host Launch` (ID `157501358279`)
- Push command: `shopify theme push --live --allow-live --store host-to-host.myshopify.com`
