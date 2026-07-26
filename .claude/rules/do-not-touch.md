---
description: Code areas that must never be modified
---

# Do Not Touch

- **Scroll-driven animations** — any CSS using `animation-timeline: view()` and `animation-range`. Fragile by design, leave exactly as-is.
- **WebGL dither** — the `#hero-dither` canvas element and all its associated JS. This is the background visual effect on index.html.
- **`img-scale-in` CSS animation** — used on case study images for entrance effect.
