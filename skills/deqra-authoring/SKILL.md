---
name: deqra-authoring
description: Author and revise Deqra presentations, documents, drawings, and pages through hosted MCP tools. Use when the user wants decks, docs, drawings, pages, PDF/PPTX/HTML export, or publish proposals on deqra.ai.
---

# Deqra authoring (Claude)

Hosted MCP: `https://mcp.deqra.ai/` (OAuth already connected — never ask for `dqag_` tokens).

Human docs: https://deqra.ai/docs/claude

## Core rules

1. After `deqra_open` / `deqra_store` / `deqra_build`, keep using **`packageId`** (not re-opening by path unless they ask).
2. Read `result.context` when present (outline, brandAccent, brandRulesExcerpt, catalogHints, readNext).
3. Cheap loop: **validate → layoutOnly → contactSheet only if needed**.
4. Publishing is optional. Files-only → `deqra_export` / `deqra_pack`. Live address → `deqra_propose` or `deqra_propose_revision`.
5. One job per slide/section; prefer Catalog families over equal card grids.
6. Claude chat tokens ≠ Deqra credits (table below).

## Credits (artifact owner’s Studio ledger)

| Work | Credits |
| :--- | ---: |
| layoutOnly / validate / edit / store / open / reads / catalog / propose status | 0 |
| `deqra_render` + `contactSheet: true` | 1 |
| `deqra_render` full (no layoutOnly/contactSheet) | 2 |
| `deqra_export` | 3 |
| Cached / failed tool | 0 |

Remind the user before contact sheet / full render / export.

## Tool map

### Discover / read

| Tool | When |
| :--- | :--- |
| `deqra_repository` | No args → owned handles (wait for choice). `{ repository }` → list public artifacts on that handle. |
| `deqra_open` | `{ repository, slug }` hosted, or `{ packageId }` / `{ documentId }` / `{ proposalId }` to resume store. |
| `deqra_info` | Title, type, page/slide counts. |
| `deqra_ls` | List package files. |
| `deqra_read` | Read a package file (e.g. `brand-rules.md`, `ai-context/AGENT.md`). |
| `deqra_validate` | Schema + catalog diversity hints. |
| `deqra_catalog` | Prefer `{ id }` for one Catalog example. |
| `deqra_example` | Load a finished sample package. |
| `deqra_kit_docs` | Authoring quickstart (content on by default). |
| `deqra_template_sample` | Document template content sample. |

### Create / edit

| Tool | When |
| :--- | :--- |
| `deqra_build` | Brief → package. `title`, optional `accent`, `slides:[{ job, title, points?, family? }]`, or document template id. |
| `deqra_store` | Persist JSON / continue with packageId. |
| `deqra_template_create` | Create from a Document template. |
| `deqra_edit` | Typed ops by **node id**: `set_text`, `set_data`, `set_metric`, `set_table`, `insert_child`, `remove_node`, `move_node`, … Prefer `dryRun: true` first when risky. |
| `deqra_import` | Import `.deq` (`deqBase64` / `deqUrl` / `uploadId`). |
| `deqra_put_asset` | Add font/image under `assets/…`. |
| `deqra_upload_url` / `deqra_upload_chunk` | Large uploads before import. |

### Paint / ship

| Tool | When |
| :--- | :--- |
| `deqra_render` | `layoutOnly: true` first (0 cr). Then `contactSheet: true` (1 cr, default scale 0.5). Full render = 2 cr. |
| `deqra_export` | `pdf` / `pptx` / `html` / `svg` as needed (3 cr). After propose, PDF may already be in `downloads[]`. |
| `deqra_pack` | Pack `.deq` (usually 0 cr). |
| `deqra_propose` | **New** live address: `{ packageId, repository, slug, note? }` → receipt + `downloads[]`. |
| `deqra_propose_revision` | Edits to **existing** live slug — never invent `.v2`. |
| `deqra_proposal_status` | Poll `{ proposalId }`. |

`deqra_unpack` is local-only and not exposed on hosted MCP.

## Happy paths (with examples)

### A. New presentation from a brief

User: “Make an 8-slide pitch about our Q1 launch, then PDF.”

```text
1. deqra_build({
     title: "Q1 Launch",
     accent: "#E8622A",
     slides: [
       { job: "cover", title: "Q1 Launch", points: ["Ship the product story"] },
       { job: "problem", title: "The gap", points: ["…"] },
       …
     ]
   })
2. deqra_validate({ packageId })
3. deqra_render({ packageId, layoutOnly: true })
4. deqra_render({ packageId, contactSheet: true })   # if they want visuals; 1 credit
5. deqra_export({ packageId, pdf: true })            # 3 credits
```

Optional publish:

```text
deqra_repository()                    # show owned handles; wait
deqra_propose({ packageId, repository: "jane.doe", slug: "q1-launch", note: "First draft" })
# fetch receipt.downloads[] into the session when possible
```

### B. Document or page

User: “One-pager product brief as a Deqra document.”

```text
1. deqra_kit_docs() or deqra_catalog / deqra_example for document patterns
2. deqra_build({ title, artifactType/document fields per tool schema }) 
   or deqra_template_create / deqra_template_sample when a template fits
3. deqra_validate → layoutOnly → export pdf
```

### C. Drawing

User: “Architecture drawing of our MCP flow.”

```text
1. deqra_catalog / deqra_example for drawing families (shapes, rels)
2. deqra_build or store a drawing document.json (no coordinates — Deqra lays out)
3. validate → render/export as needed
```

### D. Edit a live public artifact

User: “Open jane.doe/pitch-deck and change the title on slide 1.”

```text
1. deqra_open({ repository: "jane.doe", slug: "pitch-deck" })
2. Use context/outline to find node ids (or deqra_read / validate)
3. deqra_edit({
     packageId,
     dryRun: true,
     operations: [{ op: "set_text", nodeId: "h:title", text: "New title" }]
   })
4. deqra_edit({ packageId, operations: […same…] })   # without dryRun
5. deqra_propose_revision({ packageId, repository: "jane.doe", slug: "pitch-deck", note: "Title tweak" })
6. Fetch downloads[]; poll deqra_proposal_status if needed
```

### E. Browse only

```text
deqra_repository({ repository: "jane.doe" })
deqra_open({ repository: "jane.doe", slug: "…" })
deqra_info / deqra_validate
# stop — no export unless asked
```

## Propose vs export

| Goal | Tools |
| :--- | :--- |
| Download PDF/PPTX/HTML only | `deqra_export` |
| Pack `.deq` | `deqra_pack` |
| New public address | `deqra_repository()` → wait → `deqra_propose` |
| Change existing slug | `deqra_propose_revision` (same slug) |

Owner reviews proposals on deqra.ai (change-by-change). Do not claim the live site updated until accepted.

## Design quality (quick)

- One primary headline + short support line per slide/section.
- Match Catalog family to the job (hero, comparison, process, metrics, quote — not every slide as three equal cards).
- Light surfaces → dark ink; dark bands → light ink.
- After edits, re-validate before expensive render/export.
