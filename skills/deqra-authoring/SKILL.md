---
name: deqra-authoring
description: Author and revise Deqra presentations, documents, drawings, and pages through hosted MCP tools. Use when the user wants decks, docs, drawings, pages, PDF/PPTX export, or publish proposals on deqra.ai.
---

# Deqra authoring (Claude)

## Setup

1. Ensure the Deqra connector is connected (OAuth to `https://mcp.deqra.ai/`).
2. Prefer packageId across calls after `deqra_open` / `deqra_store` / `deqra_build`.
3. Docs: https://deqra.ai/docs/claude

## Credits (Deqra ledger, not Claude tokens)

| Work | Credits |
| :--- | ---: |
| layoutOnly / validate / edit / store / reads | 0 |
| contact sheet (`deqra_render` + contactSheet) | 1 |
| full render | 2 |
| `deqra_export` | 3 |

## Happy paths

### New deck from a brief

1. `deqra_build` with title + slides (job, title, points).
2. `deqra_validate`.
3. `deqra_render` with `layoutOnly: true` first.
4. `deqra_render` with `contactSheet: true` if you need visuals.
5. Optional publish: `deqra_repository()` with no args → show owned handles → wait → `deqra_propose`.
6. Or `deqra_export` for PDF/PPTX only (no publish).

### Edit a live public artifact

1. `deqra_open({ repository, slug })`.
2. `deqra_edit` by node id (`set_text`, `set_data`, …).
3. `deqra_propose_revision` — do **not** invent a `.v2` slug.
4. Fetch `downloads[]` URLs from the receipt into the session when possible.

### Browse

1. `deqra_repository({ repository })` for a public handle.
2. `deqra_catalog` / `deqra_example` / `deqra_kit_docs` before inventing structure.

## Rules

- One job per slide; prefer Catalog families over equal card grids.
- After open/store/build, read `result.context` (outline, brandAccent, catalogHints).
- Publishing is optional; export alone is fine when they only want files.
- Never ask the user for raw `dqag_` tokens — OAuth is already established.
