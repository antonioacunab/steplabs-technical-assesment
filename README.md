# Shopify Theme — Step Labs Technical Test

A modular, performance-focused Shopify theme built on top of Dawn. Developed as part of a technical assessment for Step Labs.

## Stack

- Liquid + HTML5 semantic markup
- CSS (BEM methodology, encapsulated per component via `{% stylesheet %}`)
- Shopify Theme Editor (Dynamic Blocks + Schema — zero hardcoded content)

## Getting started

### Prerequisites

- [Shopify CLI](https://shopify.dev/docs/api/shopify-cli)
- [Shopify Liquid VS Code Extension](https://shopify.dev/docs/storefronts/themes/tools/shopify-liquid-vscode) (optional but recommended)

### Setup

```bash
git clone git@github.com:antonioacunab/steplabs-shopify-technical-assesment.git
cd steplabs-shopify-technical-assesment
shopify theme dev
```

## Theme architecture

```
.
├── assets          # Critical CSS and static assets
├── blocks          # Reusable, nestable theme blocks
├── config          # Global theme settings schema and data
├── layout          # Page layout wrappers (theme.liquid, password.liquid)
├── locales         # Translation files (i18n)
├── sections        # Modular full-width page sections
├── snippets        # Reusable Liquid fragments (not directly editable in Theme Editor)
└── templates       # JSON templates defining page structure
```