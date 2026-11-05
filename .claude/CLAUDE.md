# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Personal portfolio site for Andrey Beregovoy (product designer). Static HTML pages, no build step, no bundler, no npm, no package.json — open any `.html` file directly or serve statically.

- `index.html` — one vertical scroll: hero (name | menu | statement), 2x2 case cards, "Обо мне" (photo with tilt, bio, tool logos, experience, three testimonials), footer. `Кейсы` / `Обо мне` in the menu are in-page anchors (`#cases`, `#about`)
- `rutube.html`, `auditors_monitoring.html`, `installation_panel.html`, `design_system.html` (shown as "Дизайн-система EchoTwin AI") — case pages, see "Case pages" below. Rutube is a learning project from Ilona Sarkisova's course (the page says so)
- There is no English version: the old `*-en.html` pages and the language switch were removed on purpose
- `/cases` redirects to `/#cases` (`vercel.json`)
- `Береговой_Андрей_CV.pdf` — the current CV, linked URL-encoded from the menu and the footer (the file name is Cyrillic, NFC)
- `fonts/` — self-hosted Onest (variable, weight 400–500, SIL OFL 1.1), one woff2 per unicode-range subset (cyrillic, latin)
- `photo-about.jpg/.webp` — the square crop used in "Обо мне" (600x600); `IMG001.jpg` (untracked) is its original
- `projects/` — images per case (`rutube/`, `auditors_monitoring/`, `installation_panel/`, `design_system/`), `projects/covers/` has the 4:3 card covers (1600x1200). Each case folder has `hero-wide.jpg/.webp` (2760x1380 composed cover); `rutube/plans.png` and `payment-success.png` are only the sources of its cover. Some filenames contain spaces, reference them URL-encoded (e.g. `01%20overview%20map.png`). Images ship as `<picture>` with a `.webp` source + PNG/JPEG fallback — when replacing a source image, regenerate **both**
- The tool logos on the main page are inline SVG (no logo files)

## Tech stack

- Pure HTML + CSS + vanilla JS, all inline in each `.html` file — no shared JS/CSS files, so a fix usually needs to be applied per page (five Russian pages share the same base CSS)
- No Tailwind, no Google Fonts: Onest is self-hosted via `@font-face` with `unicode-range`
- Hosted on Vercel, domain `beregovoy.design` — auto-deploys on push to `main`; other branches get a preview deploy. `vercel.json` sets `cleanUrls: true` (the site serves `/rutube`, internal links still use the `.html` filename) and the `/cases` → `/#cases` redirect

## Commands

No build/lint/test tooling exists in this repo. To preview changes, open the `.html` file directly in a browser (no dev server required).

## Design system

- One dark theme. Page background `#1b1b1a` with a weak monochrome grain: a fixed `body::before` layer made of two SVG `feTurbulence` tiles (light and dark specks, alpha baked into the colour matrix), no image files. No gradients or blobs — the owner rejected them
- Text `#f4f4f2`, muted `#8f8f8a`, secondary text `#b8b8b3`, hairlines `rgba(244,244,242,.12)`, corner radius 10px. No accent colour on the page
- Font: Onest only, weights 400 and 500. Sizes: 15 (meta, captions), 16, 17 (quotes), 18 (body), 22 (case titles, figures). The type scale is deliberately flat; the impeccable `flat-type-hierarchy` rule is ignored on the owner's decision
- Grid: three equal columns, 30px side padding and 32px gap (20px padding on mobile, breakpoint `max-width: 900px`). Labels sit in column 1, content in columns 2–3
- Short words (в, и, с, к, от, на, по…) are glued to the next word with `&nbsp;`; hyphenated words are wrapped in `.nw`. Keep doing this for new copy
- Links: native cursor, no custom cursor, no theme switch, no preloader. Menu links hug their text (the menu is a flex column with `align-items: flex-start`) so only the word reacts to hover
- Every figure and claim must already exist in the cases or the CV, never invented. Unknown facts are asked for, not guessed

## Main page notes

- A reload starts at the top: the head script drops a leftover `#cases` and keeps re-applying `scrollTo(0)` (instantly, `html` has smooth scrolling) until the first real input. Only `navType === 'reload'`, so the back button keeps restoring
- The photo tilts toward the cursor (`.photo-tilt` perspective wrapper + `.photo-wrap`); off on touch devices and with reduced motion
- Tool logos are inline SVG in muted grey; on hover Claude and Claude Code turn `#d97757`, Codex `#7c6fff`, ChatGPT white, Figma swaps to its five-colour mark

## Case pages

The four case pages share one template (duplicated inline). Structure, borrowed from peers' case studies (Foliobin MCP research):

1. Close button (fixed, top-right, returns to `index.html#cases`); no menu and no name on case pages
2. Hero as a plain vertical stack: title (22px, one line), overview, `Роль` / `Команда` (or `Контекст`), two key figures
3. Wide cover (`hero-wide`)
4. `Проблема и решение` as aligned pairs (problem left, solution right; stacked with small labels on mobile)
5. `Результат`, then details (Rutube: research, users, CJM, JTBD, testing; short cases: `Как работал`), then `Экраны`
6. `Экраны`: a pinned stage. `.pin` is `var(--stage) + var(--pan)` tall, `.pin-stick` is `position: sticky` and centred by JS (`--top`), JS translates `.track` by the scrolled distance (one scrolled px = one panned px). Card height is fixed per page through `--h` so all captions align; each figure sets its own aspect ratio in `--ar`. At `max-width: 900px` and with `prefers-reduced-motion` the stage is a normal horizontally scrolling rail
7. `Моя роль`, `Что дальше`, `Ещё кейсы` (the other three cases), footer

## User preferences

- Verify UI changes by rendering them headless and reading the screenshots yourself (see `.claude/rules/workflow.md`); don't open a visible browser window
- Do not add comments explaining what code does — only add comments for non-obvious constraints or workarounds
- Commit after each logical change, push is separate
- Prefer structurally different options over variations when exploring; the owner decides visually
