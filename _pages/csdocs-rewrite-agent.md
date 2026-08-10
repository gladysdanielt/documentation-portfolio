---
title: "CSDocs Rewrite Agent"
permalink: /samples/csdocs-rewrite-agent/
author_profile: false
toc: true
---

## The Problem

Rewriting an existing doc page well is a different problem from writing one from scratch. You're not inventing information — you're preserving exact technical meaning while making the language clearer and more parseable, and it's easy for a rewrite pass to accidentally change a flag name, drop a warning, or introduce a detail that was never in the source.

## What I Built

A deterministic Claude Code agent that reviews one Contentstack documentation page at a time and produces a technically faithful rewrite — clearer for humans, more parseable for machines (LLMs, search, retrieval) — with zero tolerance for fabrication.

### How It Works

Every session opens by asking for a source: a URL, or pasted content if access is restricted. It won't start rewriting without one, and if a URL can't be fetched, it says so and asks for the content to be pasted instead of guessing at what the page might say.

From there it runs a fixed pipeline, always in the same order:

1. **Classify** — Tutorial, How-to guide, Reference, or Conceptual/explanation. The type drives the structure of the rewrite.
2. **Audit** — reviews the page for clarity and technical accuracy.
3. **Identify risks** — flags anything ambiguous, contradictory, outdated, or technically suspect instead of silently fixing it.
4. **Rewrite** — produces the technically faithful rewrite.
5. **Self-check** — verifies the rewrite before returning it.

Large pages are processed section by section, preserving the original order, rather than merged or reorganized.

### Guardrails

The constraints matter more than the rewrite mechanics:

- Technical meaning never changes, and procedural steps are never added, removed, or reordered.
- Nothing gets renamed — APIs, parameters, fields, endpoints, CLI flags, file names, paths, env vars, error codes, and version numbers all stay exactly as written.
- Nothing gets invented. It can infer things like reading time or skill level, but has to state how it inferred them and flag the uncertainty.
- Warnings, notes, cautions, prerequisites, and deprecation notices never get silently dropped — they can be relocated or restyled, never removed.
- Code samples are untouched except for trivial whitespace, and only when behavior is provably identical; anything else gets flagged rather than changed.
- The original audience and scope are preserved — an admin doc doesn't quietly become a developer guide.

docs.contentstack.com is treated as the canonical source for related API references and FAQ discovery during the audit.

## When to Use It

For getting a clean, faithful rewrite or audit of a single existing Contentstack doc page — not for drafting brand-new documentation from scratch, which is a different problem with different guardrails.
