---
description: Export the current Deqra packageId to PDF, PPTX, and/or HTML
---

Use the Deqra MCP tools:

1. Confirm you have a `packageId` from a recent `deqra_open`, `deqra_build`, or `deqra_store`.
2. Call `deqra_export` with the formats the user wants (`pdf`, `pptx`, and/or `html` — set each to `true`). Default to `pdf: true` if they only say “export.”
3. Remind the user this costs **3** Deqra credits on success (per export call).
4. Return the `files[].url` download links clearly.
