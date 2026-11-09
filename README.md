# Deqra Claude plugin

**Deqra** is an artifact repository for presentations, documents, drawings, and pages. This plugin teaches Claude how to open repos, author packages, propose live addresses, and export — via hosted MCP.

Public packaging only — **no** Deqra engine, Spring, or paint runtime.

This folder is meant to be pushed to a **separate public GitHub repo** (`mehmetsen80/deqra-claude-plugin`). Do not publish the DeqraApp monorepo.

## What it contains

- Plugin manifest + **authoring skill** (repository mental model, full tool map, credits, happy paths)
- Slash commands: `deqra-build`, `deqra-open`, `deqra-propose`, `deqra-export`
- Remote MCP reference to `https://mcp.deqra.ai/` (URL only)
- License

The paint engine and Spring app stay private — this repo is packaging + guidance only.

## Install / listing

1. Hosted MCP must already work as a Claude custom connector or Directory connector.
2. Submit this repo as a **Plugin bundle** at [claude.ai/directory/manage](https://claude.ai/directory/manage) from the same Claude org as the connector.
3. Pair the plugin listing with the Deqra MCP connector listing.

## Local preview

Point Claude Desktop / Claude.ai plugin install at this repository once it is public.

## Credits

Claude tokens: Anthropic. Deqra paint/export: owner Studio credits (contact sheet 1 · full render 2 · export 3). See https://deqra.ai/docs/claude
