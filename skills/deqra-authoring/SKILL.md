---
name: deqra-authoring
description: >
  Author and revise Deqra artifact-repository packages (presentations, documents,
  drawings, pages) through hosted MCP. Use whenever the user mentions Deqra, a
  handle/slug, .deq artifacts, propose/publish on deqra.ai, contact sheets, or
  PDF/PPTX/HTML export from Deqra.
---

# Deqra authoring (Claude)

## What Deqra is

**Deqra is an artifact repository**, not a one-shot slide generator.

- A **repository** is a handle (e.g. `jane.doe`) that owns public (and private) packages.
- An **artifact** lives at `repository/slug` — a semantic `.deq` package (JSON + brand + catalog), not an opaque PowerPoint blob.
- Artifact types: **presentation**, **document**, **drawing**, **page**.
- Hosted paint/export runs on Deqra’s servers; Claude drives tools over MCP.

Hosted MCP: `https://mcp.deqra.ai/` (OAuth already connected — **never** ask for `dqag_` tokens).

Human docs: https://deqra.ai/docs/claude

## Session mental model

```text
repository (handle)
  └── artifact (slug)  →  open/build/store  →  packageId (this chat)
                            ├── validate / edit / catalog
                            ├── render (layoutOnly → contactSheet → full)
                            ├── export / pack
                            └── propose / propose_revision → live address
```

1. After `deqra_open` / `deqra_store` / `deqra_build`, keep using **`packageId`**. Do not re-open by path unless they ask.
2. Read `result.context` when present (outline, brandAccent, brandRulesExcerpt, catalogHints, readNext).
3. Cheap loop: **validate → layoutOnly → contactSheet only if needed**.
4. Publishing is optional. Files-only → `deqra_export` / `deqra_pack`. Live address → `deqra_propose` or `deqra_propose_revision`.
5. One job per slide/section; prefer Catalog families over equal card grids.
6. Claude chat tokens ≠ Deqra Studio credits (table below). Remind before paid work.

## Credits (artifact owner’s Studio ledger)

| Work | Credits |
| :--- | ---: |
| layoutOnly / validate / edit / store / open / reads / catalog / propose status | 0 |
| `deqra_render` + `contactSheet: true` | 1 |
| `deqra_render` full (no layoutOnly/contactSheet) | 2 |
| `deqra_export` | 3 |
| Cached / failed tool | 0 |

Warn before contact sheet / full render / export. Do not burn credits “to be helpful.”

## Tool map

### Discover / read

| Tool | When |
| :--- | :--- |
| `deqra_repository` | No args → owned handles (**wait for choice**). `{ repository }` → list artifacts on that handle. |
| `deqra_open` | `{ repository, slug }` hosted, or `{ packageId }` / `{ documentId }` / `{ proposalId }` to resume. |
| `deqra_info` | Title, type, page/slide counts. |
| `deqra_ls` | List package files. |
| `deqra_read` | Read a package file (e.g. `brand-rules.md`, `ai-context/AGENT.md`). |
| `deqra_validate` | Schema + catalog diversity hints. |
| `deqra_catalog` | Prefer `{ id }` for one Catalog example — do not invent layouts. |
| `deqra_example` | Load a finished sample package. |
| `deqra_kit_docs` | Authoring quickstart (content on by default). |
| `deqra_template_sample` | Document template content sample. |

### Create / edit

| Tool | When |
| :--- | :--- |
| `deqra_build` | Brief → package. `title`, optional `accent`, `slides:[{ job, title, points?, family? }]`, or document fields. |
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

## Decide the path first

| User intent | Start with |
| :--- | :--- |
| “What’s in my / their Deqra?” | `deqra_repository` → list → wait |
| “Open X” / paste `handle/slug` | `deqra_open` |
| “Make a deck / doc / drawing” | Clarify type → `deqra_build` (or template) |
| “Change the title / fix slide 3” | Open or use packageId → `deqra_edit` (dryRun if risky) |
| “PDF / PPTX only” | validate → optional layoutOnly → `deqra_export` |
| “Publish / put it live” | `deqra_repository()` if handle unknown → `deqra_propose` |
| “Update the live one” | `deqra_propose_revision` same slug |

Never skip to full render or export unless they asked for visuals or a file.

## Happy paths (with examples)

### A. Browse a repository

User: “What’s in jane.doe?”

```text
1. deqra_repository({ repository: "jane.doe" })
2. Summarize titles / types / slugs — wait if they need to pick one
3. deqra_open only after they choose (or clearly named a slug)
```

### B. New presentation from a brief

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
4. deqra_render({ packageId, contactSheet: true })   # only if they want visuals; 1 credit
5. deqra_export({ packageId, pdf: true })            # 3 credits
```

Optional publish:

```text
deqra_repository()                    # show owned handles; wait
deqra_propose({ packageId, repository: "jane.doe", slug: "q1-launch", note: "First draft" })
# fetch receipt.downloads[] into the session when possible
```

### C. Document or page

User: “One-pager product brief as a Deqra document.”

```text
1. deqra_kit_docs() or deqra_catalog / deqra_example for document patterns
2. deqra_build({ title, artifactType/document fields per tool schema })
   or deqra_template_create / deqra_template_sample when a template fits
3. deqra_validate → layoutOnly → export pdf
```

### D. Drawing

User: “Architecture drawing of our MCP flow.”

```text
1. deqra_catalog / deqra_example for drawing families (shapes, rels)
2. deqra_build or store a drawing document.json (no coordinates — Deqra lays out)
3. validate → render/export as needed
```

### E. Edit a live public artifact

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

## Propose vs export

| Goal | Tools |
| :--- | :--- |
| Download PDF/PPTX/HTML only | `deqra_export` |
| Pack `.deq` | `deqra_pack` |
| New public address | `deqra_repository()` → wait → `deqra_propose` |
| Change existing slug | `deqra_propose_revision` (same slug) |

Owner reviews proposals on deqra.ai (change-by-change). Do **not** claim the live site updated until accepted.

## Design quality

- One primary headline + short support line per slide/section.
- Match Catalog family to the job (hero, comparison, process, metrics, quote — not every slide as three equal cards).
- Light surfaces → dark ink; dark bands → light ink.
- Name the Catalog pattern in notes when you choose one.
- After edits, re-validate before expensive render/export.

## Anti-patterns (do not)

- Treat Deqra as “generate a PPTX and forget the repository.”
- Invent a new slug like `pitch-deck-v2` for revisions — use `deqra_propose_revision`.
- Ask for API tokens when OAuth is already connected.
- Full-render or export without asking / warning about credits.
- Guess node ids — use outline / context / validate / read.
- Place coordinates on drawings — describe nodes/edges; Deqra lays out.
- Clone equal card rows for every slide when Catalog has a better family.
- Re-open by path every turn when you already have `packageId`.
