# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Personal portfolio site for Andrey Beregovoy (designer). Six static HTML pages, no build step, no bundler, no npm, no package.json — open any `.html` file directly or serve statically.

- `index.html` — landing page: full-viewport hero with name + subtitle only
- `cases.html` — case grid (`Cases` in nav), horizontally-scrolling `.case-item` cards linking to the three case study pages
- `about.html` — bio page (`About` in nav): manifesto, photo + two-column bio text, stats row
- `rutube.html`, `auditors_monitoring.html`, `installation_panel.html` — case study detail pages
- `favicon.svg` — orange rounded square with cursor ring + dot; `favicon-light.png`/`favicon-dark.png` swap via `prefers-color-scheme`
- `projects/` — images organised per case study (`rutube/`, `auditors_monitoring/`, `installation_panel/`); some filenames contain spaces, reference them URL-encoded (e.g. `01%20overview%20map.png`). Thumbnails/photos ship as `<picture>` with a `.webp` source + PNG/JPEG fallback — when replacing a source image, regenerate **both**, the `.webp` is what most browsers actually load
- `logos/` — third-party tool logos (Claude, Figma, Lovable, Manus) used in the skills/tools section

## Tech stack

- Pure HTML + CSS + vanilla JS, all inline in each `.html` file — no shared JS/CSS files, so a fix usually needs to be applied per-page
- Tailwind CSS via CDN (only on `index.html`)
- Fonts: Jost, Montserrat, Roboto Mono from Google Fonts (see Typography below) — `rutube.html` is the one exception, still on an older Archivo/Space Grotesk/Fira Code system pending its own rework
- Hosted on Vercel, domain `beregovoy.design` — auto-deploys on push to `main`. `vercel.json` sets `cleanUrls: true` (site serves `/cases` not `/cases.html`; internal links still use the `.html` filename and are matched extension-agnostically by the nav JS, see below)

## Commands

No build/lint/test tooling exists in this repo. To preview changes, open the `.html` file directly in a browser (no dev server required).

## CSS architecture

### Typography
Three Google Fonts, each with a fixed role — don't mix roles or introduce new sizes ad hoc, reuse these:
- **Jost** — body text and UI labels. Nav links, small uppercase labels (e.g. `.hero-col-label`, `ОБЗОР`/`ЗАДАЧА`-style column headers): weight 300 (Light), 16px, uppercase, 0 letter-spacing. Body paragraphs/lists (`.bio-col`, `.manifesto-text`, `.hero-col-body`): weight 300 (Light), 20px, line-height 1.2 (120%), 0 letter-spacing. Section/slide headings one step up from a label (`.manifesto-label`, case-study intro headings): weight 200 (ExtraLight), 24px, uppercase, 0.08em (8%) letter-spacing.
- **Montserrat** — display titles only (`.page-title`, `.stat-num`, case-study `.slide-title`). Weight 200 (ExtraLight), uppercase, `font-kerning: none`, line-height ~0.9 (90%), letter-spacing ~0.02–0.03em (2–3%). Sized in **`vw`, not px** (e.g. `.page-title{font-size:8.2vw}`) so it scales proportionally with viewport — the one exception to the "fixed px" rule below.
- **Roboto Mono** — numeric counters (`#preload-count`) and small tracked mono labels only; used sparingly.

Every size above is fixed px, calibrated at a 1440px reference width — only Montserrat display titles use `vw`. This mirrors the fluid-grid technique (`fr`-based multi-column layouts) used elsewhere in the CSS: proportions are pixel-exact at 1440px and scale from there.

### Theme system
Light theme is the default (`:root`). Dark theme applied via `html.dark`, toggled by the `.theme-switch` LIGHT|DARK buttons and persisted in `localStorage['bvg_theme']`. All colours are CSS variables (`--bg`, `--fg`, etc.) redefined per-theme. An early blocking `<script>` in `<head>` (before first paint, on every page) reads `bvg_theme` and adds `html.dark` immediately, to avoid a flash of the wrong theme. Accent colour: `#e05c18`.

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

## Page navigation & transitions

`index.html`, `cases.html`, `about.html` share a hand-rolled intro/transition system, duplicated inline per page (no shared JS file). Understanding it requires reading the same block in all three:

- **Reload/direct-visit** plays the full intro: a counter (`#preload-count`, 000→100), then the title/name reveals, driven by `run()`.
- **Internal nav clicks** (nav-link → another of these 3 pages) skip the counter. A click handler sets `sessionStorage['bvg_nav'] = '1'` before navigating; an early `<head>` script on the destination page reads and clears that flag into `window.__bvgInternal`, and `shouldPreload()` (`return !window.__bvgInternal`) decides which path to take.
- **`cases.html`/`about.html`** use `slidePageIn()`: the entire `.page` — title *and* content together, as literal sibling DOM, not separate clones — is rigid-transformed up from off-screen in one `translateY` animation. This replaced an earlier design where title and content animated separately (via measured clone landing) and visibly collided — don't reintroduce separate clone/landing animations for title vs. content on these two pages.
- **`index.html`** intentionally still uses the older mechanic: a `#preload-name` clone measures `dx`/`dy` and slides onto `.hero-name` (`move()`), while `nav`/`.hero-subtitle`/`.theme-switch` reveal via a sweep check against the clone's position (`reveal()`). This was left as-is because the subtitle reveal is opacity-only with no competing transform, so it never had the collision bug that motivated `slidePageIn()` on the other two pages — don't unify it with `slidePageIn()` without a reason.
- `html.entering .page`/`.hero { opacity: 0 }` is the **only** pre-paint hide rule — `nav` and `.theme-switch` are deliberately *not* in it, so they render at natural opacity from the first frame and never flicker/disappear during navigation. Don't add them back to that selector.
- `html.no-motion` (set via `clearEnterState()`) forces `transition: none !important` on nav/.page(or .hero)/.theme-switch — this is the `prefers-reduced-motion` / instant-reveal path.
- Exit animation (leaving the current page) only fades the main content element (`opacity 0.35s ease`) — nav and `.theme-switch` are never faded on exit either.
- Nav self-clicks (link to the page you're already on) must call `e.preventDefault()` and no-op — otherwise it falls through to a real browser navigation that skips the `bvg_nav` flag and replays the full counter intro.

If you change this system on one page, check whether the same fix applies on the other two — there is no shared module to edit once, and (per above) `index.html` deliberately diverges from `cases.html`/`about.html`.

## Do NOT touch

- **Scroll-driven animations** (`animation-timeline: view()`, `animation-range`) — fragile, leave as-is
- **WebGL dither** (`#hero-dither` canvas + its JS) — visual background effect on index.html
- **`img-scale-in` CSS animation** — used on case study images

## User preferences

- Do not open the browser automatically — the user opens it themselves
- Do not add comments explaining what code does — only add comments for non-obvious constraints or workarounds
- Commit after each logical change, push is separate
