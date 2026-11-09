---
description: Open a Deqra repository artifact by handle/slug (or resume packageId)
---

Use Deqra MCP tools:

1. If they gave `repository` + `slug`: `deqra_open({ repository, slug })`.
2. If they only gave a handle: `deqra_repository({ repository })`, list artifacts, wait for a slug.
3. Summarize with `deqra_info` / outline from `result.context`.
4. Do not export or propose unless asked. Keep `packageId` for follow-ups.
