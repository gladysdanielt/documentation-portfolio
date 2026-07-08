---
title: "Building an MCP Server for the Docs Site"
permalink: /samples/mcp-server-project/
author_profile: false
toc: true
---

## The Problem

AI assistants are increasingly the first place people ask product questions — but they can only answer well if they can actually reach accurate, current documentation. Pasting docs into a prompt doesn't scale, and general web search often surfaces outdated or third-party content instead of the source of truth.

## What I Built

**docs-assistant** — a local [MCP (Model Context Protocol)](https://modelcontextprotocol.io) server that makes the Contentstack documentation searchable through a vector store, so any MCP-compatible client (like Claude) can query real doc content instead of guessing.

### How it works

1. **Discover** — the server reads the site's **`llms.txt`** (a curated list of key doc pages meant for AI consumption) alongside the standard **`sitemap.xml`**, giving it both a curated and a comprehensive view of what's published.
2. **Ingest** — pulls the underlying content from Contentstack via the Content Delivery and Content Management APIs.
3. **Embed** — text is sent to a dedicated embedder service and converted into vectors (default model: `sentence-transformers/all-mpnet-base-v2`).
4. **Store** — vectors persist in a **vectorized database** — Chroma by default, with FAISS as a configurable alternative.
5. **Serve** — five MCP tools expose the index to any connected client.

### Architecture

<pre class="mermaid">
flowchart TB
    CS[("Contentstack CMS<br/>CDA / CMA")]
    LLMS["llms.txt"]
    SITEMAP["sitemap.xml"]
    CRON["Scheduled Claude<br/>Cron Job"]
    INGEST["ingest tool"]
    EMBED["Embedder Service<br/>all-mpnet-base-v2"]
    VDB[("Vectorized Database<br/>Chroma / FAISS")]
    MCP["MCP Server"]
    CLIENT["MCP Client<br/>e.g. Claude"]

    CRON -->|pulls latest| LLMS
    LLMS -->|discovery| INGEST
    SITEMAP -->|discovery| INGEST
    CS -->|published entries| INGEST
    INGEST --> EMBED
    EMBED -->|vectors| VDB
    VDB --> MCP
    MCP --> CLIENT
</pre>

### The 5 tools

| Tool | Purpose |
|---|---|
| `health` | Server status, chunk count, config check |
| `query` | RAG query — finds the top matching chunks (**default top-k is 5**) and generates a grounded answer via Llama 3 |
| `ingest` | Spawns a background ingest of all Contentstack published entries into the local vector store |
| `list_doc_urls` | Lists all unique doc URLs in the vector store, optionally filtered by content type |
| `find_related_docs` | Finds docs similar to a given topic, title, or content — ranked by vector similarity, no LLM involved |

### Keeping the Index Current

Documentation doesn't stand still — other writers update the CMS constantly, and a stale vector index quietly becomes wrong. To solve this, I set up a **scheduled cron job routine in Claude** that automatically pulls the latest **`llms.txt`** whenever updates are made elsewhere in the CMS, re-triggering the `ingest` tool so the index stays in sync with what's actually published — without manual re-runs.

### Built for fail-fast reliability

Configuration is read from environment variables and validated with [Zod](https://zod.dev) at startup — the server refuses to start on invalid config rather than failing silently later in production.

## Why It Matters

- **Accuracy** — answers are grounded in the actual Contentstack content, not the model's training data or a stale cache.
- **Freshness** — the scheduled `llms.txt` pull keeps the vectorized database current as the CMS changes, without manual intervention.
- **Fail-fast config** — bad setup is caught immediately at startup, not discovered mid-incident.
- **Reusability** — once exposed as MCP tools, the same search capability works with any MCP-compatible client, not just one chat interface.

This is the kind of documentation engineering work that sits behind the docs, not on the page — infrastructure that makes the content itself more useful.

<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
<script>
  mermaid.initialize({ startOnLoad: true, theme: 'dark' });
</script>
