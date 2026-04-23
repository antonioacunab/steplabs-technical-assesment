# Shopify Theme — Step Labs Technical Assessment

A modular, performance-focused Shopify theme built on Dawn v15.4.1. Developed as part of a technical assessment for Step Labs.

**[View live store →](https://steplabs-test-antonioacunab.myshopify.com)**

---

## Stack

- Liquid + HTML5 semantic markup
- CSS — BEM methodology, encapsulated per section via `{% stylesheet %}`
- Vanilla JavaScript — scoped IIFEs, no dependencies
- Shopify Theme Editor — dynamic blocks and schema settings, zero hardcoded content

---

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

---

## Sections

### `hero-split`

50/50 split hero section. Left content panel, right full-bleed image.

**Blocks:**
- `cta` — label, URL, primary/secondary style, configurable padding per block
- `feature` — 5 built-in icons (target, arrows, shield, lightning, heart) with custom SVG textarea override

**Settings:** heading (regular + italic parts), description, star badge, guarantee strip with custom SVG override, accent color, background color, content alignment, layout width, content padding (desktop + mobile).

---

### `image-with-stats`

Science/stats section. Fixed-size image panel on the left, scrollable stats content on the right.

**Blocks:**
- `stat_group` — group label, up to 2 stats per block (context, value, description, footnote). Stat 2 is optional — leave value blank to render a single stat.
- `cta` — label, URL, default arrow icon with custom SVG override.

**Settings:** heading (regular + italic), image, optional mobile image, section background color, content alignment, layout width, content padding (desktop + mobile), count-up animation duration.

---

## Design decisions

### Layout

**`hero-split`**

The outer `<section>` carries the background color, while the grid lives on an inner `hero-split__inner` wrapper. This allows the background to extend full-width even when the content is constrained to 1440px — avoiding a visible color cutoff at the grid boundary.

**`image-with-stats`**

The left image column is fixed at `629.75px` (matching the Figma spec exactly) using `grid-template-columns: 629.75px 1fr`. The section is constrained to 1440px by default via a CSS class toggle in the schema, with a full-width option available.

---

### Schema and block patterns

CTA buttons are implemented as blocks (not settings) in both sections. This allows merchants to add, remove, and reorder them independently without touching section settings — consistent with how Shopify recommends dynamic content.

Block iteration always loops over `section.blocks` with an `if block.type == 'x'` check, rather than using the `where` filter to pre-filter. This preserves Shopify's editor context on each block, enabling direct click-to-select in the theme editor.

Disabled CTA links (no URL set) render as `<a>` without an `href` attribute rather than `href=""`. An anchor without `href` is a valid HTML5 placeholder that doesn't navigate, so `pointer-events: none` is unnecessary — and removing it keeps the block selectable in the editor.

---

### Superscript and footnote system (`image-with-stats`)

Superscripts (¹²³…) are generated automatically by indexing into a Liquid array split from a unicode string. A shared `stat_counter` variable increments each time a stat with a non-blank value is rendered. The same counter logic runs twice — once for the stat values and once for the footnotes — ensuring references always match their citations.

A pre-check loop sets a `has_footnotes` boolean before rendering the footnote container, which avoids an empty `<p>` in the DOM when no footnotes are configured.

---

### Count-up animation (`image-with-stats`)

Stat values animate from 0 to their final value when scrolled into view via `IntersectionObserver`. The animation duration is configurable via a schema range setting (500ms–5000ms) and is passed to JavaScript through a `data-animation-duration` attribute on the section element — the only reliable way to pass Liquid values into a `{% javascript %}` block, which doesn't process Liquid.

The parser handles range values like `40-50%` by extracting both numbers and animating them in parallel with the same easing curve (easeOutCubic).

---

### Entrance animations

Both sections use `@keyframes` with `animation-fill-mode: backwards` instead of CSS transitions. The `backwards` fill mode keeps each element at the animation's `from` state (opacity 0) during its delay period, regardless of when JavaScript adds the trigger class. This eliminates the race condition that occurs with transitions when the initial hidden state and the visible state are applied in the same browser paint cycle.

`hero-split` triggers immediately on page load (section is above the fold). `image-with-stats` triggers via `IntersectionObserver` at 15% viewport threshold.

Both animations are disabled when `prefers-reduced-motion: reduce` is set.

---

### Performance

- Hero image: `loading="eager"` + `fetchpriority="high"` to optimize LCP.
- Stats image: `loading="lazy"`.
- All images use `srcset` + `sizes` via Shopify's `image_url` filter for responsive delivery.
- CSS is encapsulated in `{% stylesheet %}` tags — Shopify bundles and deduplicates these, preventing global side effects.
- No shared JavaScript between sections.

---

### Accessibility

- `aria-label` on each `<section>` element.
- Decorative SVGs marked with `aria-hidden="true"`.
- `aria-disabled="true"` on CTA links without a URL.
- Semantic heading hierarchy: `h1` in hero-split, `h2` for the stats section title, `h3` for stat group labels.

---

## Known behaviours

### Custom SVG icons (`hero-split`)

Figma exports SVGs with short sequential IDs (`id="a"`, `id="b"`) and redundant `<clipPath>` wrappers that clip to the same bounds as the `viewBox`. When the same icon is pasted more than once on the page, those IDs collide and the browser misapplies clip paths — typically rendering only a corner of the icon.

**Recommended fix:** run the SVG through [SVGO](https://svgomg.net) with "Remove useless defs" and "Remove empty containers" enabled before pasting. This strips the redundant wrappers and eliminates the collision. Alternatively, remove the `<g clip-path>` wrappers and `<defs>` block manually.

For `currentColor` support (icon color inherits from the surrounding text), ensure the icon's `<path>` uses `fill="currentColor"` instead of a hardcoded hex value.

---

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