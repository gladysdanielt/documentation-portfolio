---
title: "A Personal Work Wiki, Maintained by Claude"
permalink: /samples/work-wiki/
author_profile: false
toc: true
---

*Turning scattered AI session history into a knowledge base that compounds instead of resetting*

## The Problem

Every Claude Code session I run — reviewing a docs PR, debugging a CMS sync pipeline, auditing AI-rewritten content — starts from zero. The findings, the corrections I give the model, the open threads I never finished: all of it lives inside that one session's history and nowhere else. Ask a related question in a new session three weeks later, and the model re-derives everything from scratch, if it can find it at all. Nothing accumulates. This is the standard failure mode of chat-with-your-docs tools (RAG, NotebookLM, file uploads to a chatbot): retrieval without memory.

By the time I had ~30 sessions spread across four different project folders — a reviewer-agent build, a CMS↔Git sync pipeline spanning three PRs, a content-freshness audit, a RAG assistant with a nightly ingest job — I had no single place that could answer "what's still blocking PR #56?" or "what did I already learn about ambiguous slug matching?" without me manually re-reading old transcripts.

## What It Is

A wiki that Claude reads and writes, not one I maintain by hand — based on [Andrej Karpathy's "LLM Wiki" pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). The core idea: instead of retrieving from raw sources at query time, the LLM incrementally builds and keeps current a structured set of markdown pages that sit between me and the raw history. New information gets integrated — not just indexed — into the existing pages: entity summaries get updated, contradictions get flagged, open questions get tracked in one place.

It's three layers:
- **Raw sources** — my actual Claude Code session transcripts and the PRs I've worked, untouched, addressed by ID.
- **The wiki** — markdown pages, one per task area (the reviewer agents, the sync pipeline, the RAG assistant) plus cross-cutting pages for lessons learned and open follow-ups. Claude owns this layer entirely.
- **A schema file** — tells Claude the page format and three standing operations: *ingest* a finished session into the right page, *query* the index before re-deriving anything, and *lint* the wiki periodically for staleness or contradictions.

## How It Works

The wiki lives in one project folder, seeded by having Claude read back through real session history (via session-management tools, not guesswork) and extract concrete facts — file names, PR numbers, decisions, corrections I'd given — into structured pages, an `index.md` catalog, and a chronological `log.md`. Since my work spans several project directories, each of those got a one-line pointer added to its own `CLAUDE.md`: any new session started there already knows the wiki exists and where to read/write it, rather than me re-explaining it every time.

Going forward, upkeep is a conversation, not a chore: "ingest that session," "what's blocking X," "lint the wiki" — Claude does the cross-referencing and bookkeeping that a wiki normally dies from lacking.

## Impact

This just launched, so there's no usage trend to report yet — a specific number here would be a guess, and guesses don't belong in a portfolio piece. What's already true from seeding it:

- **It surfaced a real, silent failure I hadn't noticed**: a nightly automated docs-ingest job had failed *every single night* for weeks because a dependency was never running unattended — invisible until the seeding process read across the actual run history instead of trusting the task's name.
- **Corrections I'd given piecemeal are now one reusable page**, not tribal knowledge I'd have to remember to repeat — "verify exact line numbers before posting a review comment," "never auto-resolve ambiguous matches," and four others.
- **Every stalled thread across four unrelated projects — a blocked PR, an incomplete fact-check pass — now lives in a single open-follow-ups tracker**, instead of four separate places I'd have to remember to check.

The bigger shift is in how I use AI day to day: less "ask the model and hope it remembers," more building a system where the work I do with Claude keeps getting more useful the longer I do it.
