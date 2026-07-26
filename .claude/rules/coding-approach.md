---
description: How to think and act before and during implementation
---

# Coding Approach

## Before implementing
- State assumptions explicitly. If uncertain — ask, don't guess.
- If multiple interpretations exist, surface them instead of picking silently.
- If a simpler approach exists, say so and propose it.
- If something is unclear, stop. Name what's confusing. Ask.

## Simplicity
- Minimum code that solves the problem. Nothing speculative.
- No abstractions for single-use code.
- No configurability or flexibility that wasn't requested.
- If the result is 200 lines and it could be 50 — rewrite it.

## Surgical changes
- Touch only what the request requires.
- Don't improve, reformat, or refactor adjacent code that isn't broken.
- Match the existing style even if you'd do it differently.
- If you notice unrelated dead code — mention it, don't delete it.
- Remove only the imports/variables/functions that YOUR changes made unused.

Every changed line should trace directly to the user's request.
