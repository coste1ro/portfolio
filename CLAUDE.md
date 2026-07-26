# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Personal portfolio site for Andrey Beregovoy (designer). Four static HTML pages, no build step, no bundler, no npm.

- `index.html` — main portfolio page (hero, projects carousel, skills, contact, footer)
- `rutube.html`, `auditors_monitoring.html`, `installation_panel.html` — case study pages
- `favicon.svg` — orange rounded square with cursor ring + dot
- `projects/` — images organised per case study (`rutube/`, `auditors_monitoring/`, `installation_panel/`); some filenames contain spaces, reference them URL-encoded (e.g. `01%20overview%20map.png`)

## Tech stack

- Pure HTML + CSS + vanilla JS, all inline in each `.html` file
- Tailwind CSS via CDN (only on `index.html`)
- Fonts: Inter + Fira Code from Google Fonts
- Hosted on Vercel, domain `beregovoy.design` — auto-deploys on push to `main`

## CSS architecture

### Theme system
Dark theme is default. Light theme applied via `body.light`. All colours use CSS variables defined in `:root` (dark) and `body.light` blocks. Accent colour: `#e05c18`.

### Cursor
- `* { cursor: none !important; }` hides native cursor globally on all pages
- Custom cursor: `#cursor-outer` (ring) + `#cursor-dot` — both use `mix-blend-mode: difference` so they auto-contrast against any background
- `.cursor-link` state overrides to `mix-blend-mode: normal` so "Открыть" label stays readable
- Touch devices: `@media (hover: none) and (pointer: coarse)` hides cursor elements and restores `cursor: auto !important` — this rule must come **after** `* { cursor: none !important; }` so it wins

### Responsive
Breakpoint: `@media (max-width: 720px)`. Skills grid uses `display: contents` on `.skills-col` at mobile to collapse 3-col → 1-col.

### Case study pages — shared patterns
- Meta-bar: CSS Grid `grid-template-columns: 1fr 1fr`, borders via `nth-child(2n)` / `nth-child(n+3)`
- Hero title: `white-space: normal; font-size: clamp(28px, 8vw, 48px)` at mobile
- Scroll reveal: `animation-timeline: view()` with `animation-range: entry 0% entry 45%`

## Do NOT touch

- **Scroll-driven animations** (`animation-timeline: view()`, `animation-range`) — fragile, leave as-is
- **WebGL dither** (`#hero-dither` canvas + its JS) — visual background effect on index.html
- **`img-scale-in` CSS animation** — used on case study images

## User preferences

- Do not open the browser automatically — the user opens it themselves
- Do not add comments explaining what code does — only add comments for non-obvious constraints or workarounds
- Commit after each logical change, push is separate
