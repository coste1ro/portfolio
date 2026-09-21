---
description: How to work with Andrey on this project
---

# Workflow Rules

- **Look at what you change** — render it headless (Playwright for Python driving the installed Google Chrome, no node on this machine) and read the screenshots yourself before reporting UI work as done. Don't pop up a visible browser window; Andrey opens that himself.
- **No comments explaining what code does** — only add a comment when there's a non-obvious constraint, workaround, or hidden invariant. Well-named code speaks for itself.
- **Commit after each logical change** — one thing per commit. Push is always a separate, explicit step.
- **No cursor in HTML** — `* { cursor: none !important; }` handles this globally. Don't add `cursor: pointer` to individual elements.
