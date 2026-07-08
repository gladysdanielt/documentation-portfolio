---
title: "Building an MCP Server for the Docs Site"
permalink: /samples/mcp-server-project/
author_profile: false
toc: true
---

## The Problem

AI assistants are increasingly the first place people ask product questions — but they can only answer well if they can actually reach accurate, current documentation. Pasting docs into a prompt doesn't scale, and general web search often surfaces outdated or third-party content instead of the source of truth.

## What I Built

**docs-assistant** — a local [MCP (Model Context Protocol)](https://modelcontextprotocol.io) server that makes the Contentstack documentation searchable through a vector store, so any MCP-compatible client (like Claude) can query real doc content by keyword instead of guessing.

### How it works

1. **Ingest** — content is pulled directly from Contentstack via the Content Delivery and Content Management APIs.
2. **Embed** — text is sent to a dedicated embedder service and converted into vectors (default model: `sentence-transformers/all-mpnet-base-v2`).
3. **Store** — vectors persist in a local vector store — Chroma by default, with FAISS as a configurable alternative.
4. **Serve** — the server exposes two MCP tools:

| Tool | Parameters | Description |
|---|---|---|
| `search_docs` | `keyword` (required) | Searches ingested docs by keyword, returns matching snippets from the vector store |
| `list_doc_urls` | `keyword` (optional) | Lists unique documentation URLs, optionally filtered by keyword |

### Built for fail-fast reliability

Configuration is read from environment variables and validated with [Zod](https://zod.dev) at startup — the server refuses to start on invalid config rather than failing silently later in production.

## Why It Matters

- **Accuracy** — answers are grounded in the actual Contentstack content, not the model's training data or a stale cache.
- **Fail-fast config** — bad setup is caught immediately at startup, not discovered mid-incident.
- **Reusability** — once exposed as MCP tools, the same search capability works with any MCP-compatible client, not just one chat interface.

This is the kind of documentation engineering work that sits behind the docs, not on the page — infrastructure that makes the content itself more useful.
