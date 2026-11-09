---
description: Build a new package in the Deqra artifact repository from a brief, then validate and layout-check
---

Use Deqra MCP tools:

1. Clarify artifact type (presentation / document / drawing / page) and title. Reminder: Deqra stores semantic packages in a repository — not a throwaway PPTX.
2. For presentations: `deqra_build` with `title`, optional `accent`, and `slides:[{ job, title, points?, family? }]`.
3. `deqra_validate` on the returned `packageId`.
4. `deqra_render` with `layoutOnly: true` (0 credits).
5. Only if the user wants visuals: `contactSheet: true` (1 credit) — warn first.
6. Stop unless they ask to export or propose. Keep using `packageId`.
