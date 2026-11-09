---
description: Propose a new Deqra address or a revision to a live slug
---

Use Deqra MCP tools:

1. Confirm you have a validated `packageId`.
2. **New artifact:** unless they already named an exact handle, call `deqra_repository()` with no args, show owned handles, and wait. Then `deqra_propose({ packageId, repository, slug, note? })`.
3. **Existing live slug:** `deqra_propose_revision({ packageId, repository, slug, note? })` — same slug; never invent `.v2`.
4. Prefer fetching receipt `downloads[]` (PDF / `.deq`) into the session.
5. Poll `deqra_proposal_status` if they ask whether it was accepted.
6. Remind them the owner reviews on deqra.ai before it goes live.
