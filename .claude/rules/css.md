---
description: CSS rules specific to this codebase
---

# CSS Rules

## Theme and background
One dark theme. Page colour `#1b1b1a` on `html`, `body` is transparent, and the grain is a fixed `body::before` layer with `z-index: -1`. Don't give `body` a background or the grain disappears. No gradients, no accent colour.

## Font
Onest only (`fonts/onest-cyrillic.woff2`, `fonts/onest-latin.woff2`, weight 400–500). Don't add weights outside that range and don't load fonts from a CDN.

## Layout
Three columns (`repeat(3, minmax(0, 1fr))`), 30px side padding, 32px gap; 20px padding at `max-width: 900px`. Footers and section dividers are inset by the same side padding (use `margin`, not a full-bleed border).

## Cursor
The native cursor is used everywhere on the Russian pages. The old `cursor: none` rule exists only in the `*-en.html` pages.

## Image paths
Files in `projects/auditors_monitoring/` and `projects/installation_panel/` have spaces in their names — reference them URL-encoded: `01%20overview%20map.png`. Always ship a `.webp` next to the PNG and use `<picture>`.
