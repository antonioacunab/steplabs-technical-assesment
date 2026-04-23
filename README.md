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

## Sections

### `hero-split`

50/50 split hero with configurable content panel and full-bleed image. Supports dynamic CTA and feature item blocks.

### `image-with-stats`

Science/stats section with a fixed-size image panel and dynamic stat group blocks. Includes automatic superscript generation and count-up animation on scroll.

## Known behaviours & decisions

### Custom SVG icons (`hero-split`)

Figma exports SVGs with short sequential IDs (`id="a"`, `id="b"`) and redundant `<clipPath>` wrappers that clip to the same bounds as the `viewBox`. When pasting the same icon multiple times, those IDs collide in the HTML document and the browser misapplies clip paths.

**Recommended export process:** Run the SVG through [SVGO](https://svgomg.net) with "Remove useless defs" and "Remove empty containers" enabled before pasting. This strips the redundant wrappers and eliminates the ID conflict. Alternatively, remove the `<g clip-path>` wrappers and `<defs>` block manually — they are not needed for icon rendering.

For `currentColor` support (icon color inherits from the surrounding text), ensure the icon's main `<path>` uses `fill="currentColor"` instead of a hardcoded hex value.

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