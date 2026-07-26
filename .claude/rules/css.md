---
description: CSS rules specific to this codebase
---

# CSS Rules

## Cursor ordering
`* { cursor: none !important; }` must appear **before** `@media (hover: none) and (pointer: coarse)`. Both use `* !important` — the later rule wins, so the touch override must come second to restore native cursor on touch devices.

## Theme
Dark is default (`:root`). Light theme via `body.light`. All colours are CSS variables. Accent: `#e05c18`.

## mix-blend-mode on cursor
`#cursor-outer` and `#cursor-dot` use `mix-blend-mode: difference` so the cursor auto-contrasts against any background. The `.cursor-link` state overrides to `mix-blend-mode: normal` so label text stays readable.

## Image paths
Files in `projects/auditors_monitoring/` have spaces in their names — reference them URL-encoded: `01%20overview%20map.png`.
