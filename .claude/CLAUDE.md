# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Personal portfolio site for Andrey Beregovoy (UX/Product designer). Five static HTML pages, no build step, no bundler, no npm, no package.json — open any `.html` file directly or serve statically.

- `index.html` — the one landing page, a single vertical scroll of three full-width blocks: hero (page title, photo + bio, recommendations carousel), cases (gray block, 2x2 grid of white `.case-item` cards: thumbnail, title, short overview, two key figures) and a footer (email left, Telegram right). `Кейсы` in the nav is the in-page anchor `#cases`
- `rutube.html`, `auditors_monitoring.html`, `installation_panel.html`, `design_system.html` (shown as "Дизайн-система EchoTwin AI") — case study pages, see "Case study pages" below
- `/cases` redirects to `/#cases` (`vercel.json`); `cases.html` and `about.html` no longer exist
- `favicon.svg` — orange rounded square with cursor ring + dot; `favicon-light.png`/`favicon-dark.png` swap via `prefers-color-scheme`
- `projects/` — images organised per case study (`rutube/`, `auditors_monitoring/`, `installation_panel/`); some filenames contain spaces, reference them URL-encoded (e.g. `01%20overview%20map.png`). Thumbnails/photos ship as `<picture>` with a `.webp` source + PNG/JPEG fallback — when replacing a source image, regenerate **both**, the `.webp` is what most browsers actually load
- `logos/` — third-party tool logos (Claude, Figma, Lovable, Manus) used in the skills/tools section

## Tech stack

- Pure HTML + CSS + vanilla JS, all inline in each `.html` file — no shared JS/CSS files, so a fix usually needs to be applied per-page
- Tailwind CSS via CDN (only on `index.html`)
- Fonts: Jost, Montserrat, Roboto Mono from Google Fonts (see Typography below) — `rutube.html` is the one exception, still on an older Archivo/Space Grotesk/Fira Code system pending its own rework
- Hosted on Vercel, domain `beregovoy.design` — auto-deploys on push to `main`; other branches get a preview deploy. `vercel.json` sets `cleanUrls: true` (site serves `/rutube` not `/rutube.html`; internal links still use the `.html` filename and are matched extension-agnostically by the nav JS, see below) and the `/cases` → `/#cases` redirect

## Commands

No build/lint/test tooling exists in this repo. To preview changes, open the `.html` file directly in a browser (no dev server required).

## CSS architecture

### Typography
Exactly **4 text styles** exist across the whole site — don't introduce a 5th, reuse one of these:
1. **Hero** — display titles (`.page-title`, `.stat-num`, case-study `.slide-title`). Montserrat, weight 200 (ExtraLight), uppercase, `font-kerning: none`, line-height 0.9 (90%), letter-spacing 0.03em (3%). Sized in **`vw`, not px** (e.g. `.page-title{font-size:8.2vw}`) so it scales proportionally with viewport — the one style that isn't a fixed px size.
2. **Heading** — short uppercase labels *and* one-line headings alike (`.manifesto-label` "Манифест", case-study `ОБЗОР`/`ЗАДАЧА` column labels, case-study intro headings like "Инструмент для менеджера..."). Jost, weight 200 (ExtraLight), 24px, uppercase, letter-spacing 0.08em (8%).
3. **Nav** — nav links and the LIGHT|DARK theme switch only. Jost, weight 300 (Light), 16px, uppercase, 0 letter-spacing.
4. **Body** — paragraphs and lists (`.bio-col`, `.manifesto-text`, `.hero-col-body`). Jost, weight 300 (Light), 20px, line-height 1.2 (120%), 0 letter-spacing.

Roboto Mono (weight 200) shows up only for the reload counter (`#preload-count`) — not a 5th text style, just a numeric display.

Every size above is fixed px, calibrated at a 1440px reference width — only the Hero style uses `vw`. This mirrors the fluid-grid technique (`fr`-based multi-column layouts) used elsewhere in the CSS: proportions are pixel-exact at 1440px and scale from there.

### Theme system
Light theme is the default (`:root`). Dark theme applied via `html.dark`, toggled by the `.theme-switch` LIGHT|DARK buttons and persisted in `localStorage['bvg_theme']`. All colours are CSS variables (`--bg`, `--fg`, etc.) redefined per-theme. An early blocking `<script>` in `<head>` (before first paint, on every page) reads `bvg_theme` and adds `html.dark` immediately, to avoid a flash of the wrong theme. Accent colour: `#e05c18`.

### Cursor
- `* { cursor: none !important; }` hides native cursor globally on all pages
- Custom cursor: `#cursor-outer` (ring) + `#cursor-dot` — both use `mix-blend-mode: difference` so they auto-contrast against any background
- `.cursor-link` state overrides to `mix-blend-mode: normal` so "Открыть" label stays readable
- Touch devices: `@media (hover: none) and (pointer: coarse)` hides cursor elements and restores `cursor: auto !important` — this rule must come **after** `* { cursor: none !important; }` so it wins

### Responsive
Breakpoint: `@media (max-width: 720px)`. Skills grid uses `display: contents` on `.skills-col` at mobile to collapse 3-col → 1-col.

### Case study pages
The four case pages share one vertical-scroll layout, duplicated inline per file (no shared CSS/JS, so a fix is applied four times):

- Each `<section class="slide">` is a full-viewport (`100vh`) page, one under another. On desktop they are white pages on a gray (`#e8e8e8`) canvas with a 16px gutter; mobile (`max-width: 900px`) is one stacked column instead.
- Slides with a wide widget (the screens strip, and on rutube also the tables, IA, user-flow) sit inside a `.strip-scroll` / `.pin-scroll` wrapper of `100vh + var(--pan)`. The slide is `position: sticky; top: 0`, so it pins while the page scrolls through the wrapper, and JS translates the inner track by the scrolled distance (one scrolled px = one panned px). `--pan` is the widget's overflow, set from JS; on rutube every wrapper is measured in two passes (all heights first, then all pin offsets) because each wrapper shifts the ones below it.
- The hero slide: `.hero-heading` is a **sibling** of `.hero-grid`, never a child. `.hero-grid` has its own `transform`, which makes it the containing block for absolutely positioned descendants, so a nested heading resolves `top:40px` against the grid, not the slide.
- Fit-to-viewport: content that is taller than the space between `.eyebrow` and `.slide-title` is shrunk with `scaleY(var(--fit-scale))`; positions are measured against each slide's own top edge so it holds at any scroll position.
- The close button (`.viewer-nav`) returns to `index.html#cases`; it is fixed top-right with `mix-blend-mode: difference`.
- Each case shows its two key figures (`.hero-stats`: number in the Hero style, label in the Nav style at half opacity) under the Обзор text and again on its card on the main page. Every figure must already exist in the case or the resume, never invented.

## Page navigation & transitions

`index.html` and the case pages are linked by a fade-out / fast-intro handshake, duplicated inline:

- **Reload/direct-visit** of `index.html` plays the full intro: a counter (`#preload-count`, 000→100), then the page title reveals via a clone (`#preload-title`) and the whole `.page` slides up (`slidePageIn()`).
- **Internal nav clicks** (a `.case-item` or a nav link to another page) skip the counter. The click handler fades `.page` out (`opacity 0.35s`), sets `sessionStorage['bvg_nav'] = '1'`, then navigates; an early `<head>` script on the destination reads and clears that flag into `window.__bvgInternal` (and adds `html.entering`), and `shouldPreload()` (`return !window.__bvgInternal`) picks the path. Case pages set the flag on their close link too.
- **Arriving with `#cases`** (the close button on a case page) skips the intro entirely and scrolls the block into view.
- **A reload always starts at the top.** Chrome restores the old scroll position during load (and jumps to a leftover `#cases`), which made the intro play over the cases block. The head script clears the hash and forces `scrollTo(0)` (`behavior: 'instant'`, since `html` has `scroll-behavior: smooth`) right after load, until the first real input. Only `navType === 'reload'` is touched, so the back button restores position as usual.
- `html.entering .page { opacity: 0 }` is the **only** pre-paint hide rule — `nav` is deliberately *not* in it, so it renders from the first frame and never flickers during navigation.
- `html.no-motion` (set via `clearEnterState()`) forces `transition: none !important` — the `prefers-reduced-motion` / instant-reveal path.
- Nav self-clicks (link to the page you're already on) must call `e.preventDefault()`; in-page anchors (`#cases`) are left to the browser.
- The nav is `position: absolute` in the top-right corner of the page, so it scrolls away with the content (on mobile it is the fixed frosted bar + menu toggle). Don't make it fixed again: the owner wants nothing floating over the content.

## Do NOT touch

- **Scroll-driven animations** (`animation-timeline: view()`, `animation-range`) — fragile, leave as-is
- **WebGL dither** (`#hero-dither` canvas + its JS) — visual background effect on index.html
- **`img-scale-in` CSS animation** — used on case study images

## User preferences

- Verify UI changes by rendering them headless and reading the screenshots yourself (see `.claude/rules/workflow.md`); don't open a visible browser window
- Do not add comments explaining what code does — only add comments for non-obvious constraints or workarounds
- Commit after each logical change, push is separate
