---
title: "Building an MCP Server for the Docs Site"
permalink: /samples/mcp-server-project/
author_profile: false
toc: true
---

## The Problem

AI assistants are increasingly the first place people ask product questions — but they can only answer well if they can actually reach accurate, current documentation. Pasting docs into a prompt doesn't scale, and general web search often surfaces outdated or third-party content instead of the source of truth.

## What I Built

An **MCP (Model Context Protocol) server** that gives AI assistants direct, structured access to the documentation site — so they can search and retrieve real doc content instead of guessing.

### How it works

1. **Discover pages** — the server reads the site's `llms.txt` file (a curated list of key doc pages meant for AI consumption) alongside the standard `sitemap.xml`, so it has both a curated and a comprehensive view of what's published.
2. **Ingest and chunk content** — each page is fetched and broken into smaller, coherent chunks, since retrieval accuracy depends on chunks being focused rather than whole-page dumps.
3. **Vectorize and store** — chunks are embedded and stored in a vector database, enabling semantic search (finding relevant content by meaning, not just keyword match).
4. **Expose as MCP tools** — the server exposes search and retrieval as MCP tools, so any MCP-compatible AI assistant can query the docs directly and cite real, current pages in its answers.

## Why It Matters

- **Accuracy** — answers are grounded in the actual published docs, not the model's training data or an outdated cache.
- **Freshness** — re-running ingestion against `llms.txt` and `sitemap.xml` keeps the index current as docs change.
- **Reusability** — once exposed as MCP tools, the same search capability works across any MCP-compatible assistant, not just one specific chat interface.

This is the kind of documentation engineering work that sits behind the docs, not on the page — infrastructure that makes the content itself more useful.
