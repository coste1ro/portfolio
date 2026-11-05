---
description: Code areas that must never be modified
---

# Do Not Touch

The Russian pages were redesigned and no longer contain any of the items below. They still apply to the old English pages (`*-en.html`), if you ever have to edit those:

- **Scroll-driven animations** — any CSS using `animation-timeline: view()` and `animation-range`. Fragile by design, leave exactly as-is.
- **WebGL dither** — the `#hero-dither` canvas element and all its associated JS (`index-en.html`).
- **`img-scale-in` CSS animation** — used on case study images in the English pages.
