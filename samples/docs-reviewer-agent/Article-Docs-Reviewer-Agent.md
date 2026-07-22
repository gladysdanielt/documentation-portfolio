---
title: "Building a Multi-Agent Documentation Reviewer with Claude Code"
permalink: /samples/docs-reviewer-agent/article/
layout: single
author_profile: false
toc: true
---

Reviewing developer documentation PRs is repetitive in a specific way: the same categories of issues recur — skipped authentication steps, invented API names, undocumented flags, redundant examples — but catching them reliably every time takes a trained eye and a lot of re-reading. I built a small system of specialist review agents on top of Claude Code to do this first pass consistently, so I can spend my review time on judgment calls instead of pattern-matching.

This article walks through how it's structured and why, without reproducing the actual prompts.

## The problem with a single "review everything" prompt

My first attempt was one big prompt covering grammar, structure, SDK code correctness, and CLI syntax all at once. It was mediocre at all of them — a prompt tuned to catch an invented API constructor name isn't the same shape as one tuned to catch a missing exit-code table. Splitting into **specialists**, each an expert in one documentation domain, produced sharper, more confident findings.

## Architecture

<pre class="mermaid">
flowchart TD
    A[Paste a PR link] --> B[Fetch the diff via GitHub CLI]
    B --> C{Classify changed files}
    C -->|SDK code samples,<br/>client/class instantiation| D[SDK Reviewer]
    C -->|Terminal commands,<br/>flags, exit codes| E[CLI Doc Reviewer]
    C -->|Conceptual / prose only| F[General Docs Reviewer]
    D --> G[Verify claims against<br/>real SDK source]
    E --> H[Verify claims against<br/>real CLI behavior]
    F --> I[Review IA, redundancy,<br/>developer experience]
    G --> J[Draft inline PR comments]
    H --> J
    I --> J
    J --> K{Show findings to reviewer}
    K -->|Confirmed| L[Post as a PENDING GitHub review]
    K -->|Adjust| J
    L --> M[Human checks, edits,<br/>submits on GitHub]
</pre>

A router (a small routing document, not a model) reads the diff, decides which specialist domain each changed file belongs to, and dispatches accordingly. A single PR can trigger more than one specialist if it touches both an SDK page and a CLI page.

## The specialists

Three reviewers, each scoped to one kind of failure mode:

**SDK Reviewer** — reviews SDK reference/how-to pages: does the example show the full install → init → auth → call → response chain, does it follow the target language's idioms (context managers in Python, try-with-resources in Java, and so on), and — the highest-priority check — does every class and method name it uses actually exist in the real SDK, or does it look like a plausible-but-invented name a generative pass produced and nobody ran.

**CLI Doc Reviewer** — reviews CLI reference/how-to pages against a developer-journey model (overview → prerequisites → install → auth → quick start → command reference → output → examples → troubleshooting), with automation correctness treated as a high-priority area: incorrect exit-code handling and unsafe scripting patterns get flagged the same way a wrong command would.

**General Docs Reviewer** — the fallback for conceptual or prose-heavy pages: information architecture, redundancy (its strongest category — repeated setup instructions, repeated examples), and scope creep (implementation details leaking into user-facing docs).

All three share the same constraints: three severity levels only (Critical / Important / Nice to Have), never rewrite the document, ignore grammar and style nits entirely, and every comment follows the same shape — **Title, why it matters to a developer, a concrete recommendation.**

## Guardrails that matter more than the prompts

Three rules apply across every specialist, and they're the part I'd emphasize to anyone building something similar:

1. **Diff-only scope.** Each reviewer only comments on lines the PR actually changed. Pre-existing content is read for context — to check whether authentication was already documented earlier on the page — but is never itself flagged. Without this, a small PR triggers a review of the entire page's history, which isn't useful and isn't fair to the author.

2. **Verify before flagging Critical.** The single most damaging false positive is telling someone their example uses a real API when it doesn't, or the reverse. Before marking anything Critical, the agent checks the real source — the actual SDK's method signatures, the CLI's real flags — rather than trusting surface plausibility.

3. **Pending, never submitted.** This is the one I consider non-negotiable.

<pre class="mermaid">
sequenceDiagram
    participant You
    participant Agent
    participant GitHub

    You->>Agent: Paste PR link
    Agent->>GitHub: Fetch diff (read-only)
    GitHub-->>Agent: Diff + file list
    Agent->>Agent: Classify, review, verify against source
    Agent->>You: Show proposed comments
    You->>Agent: Confirm
    Agent->>GitHub: Create review (state: PENDING)
    Note over GitHub: Comments exist but are<br/>invisible to other collaborators
    You->>GitHub: Review, edit, delete anything irrelevant
    You->>GitHub: Submit review yourself
</pre>

Every comment lands as a **draft** review on GitHub — visible only to me, editable, deletable — until I personally hit submit. The agent never approves, requests changes, or submits on its own, and it asks for confirmation again before it ever calls the GitHub API. A documentation review is still a judgment call at the end; automation should hand you a strong first draft, not a decision.

## Result

Tested against a real PR adding entry-variant support to a .NET SDK's docs, the pipeline cross-checked the new examples against the actual SDK source on GitHub and surfaced two genuine gaps a surface read would've missed: an example using a valid-but-undocumented parameter combination, and a validation note present on an async method's docs but silently missing from its synchronous twin. Both were real, both were small, and both were exactly the kind of thing that's easy to miss on a fifth review pass of the day — which is the actual point of building this.

<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
<script>
  mermaid.initialize({ startOnLoad: true, theme: 'dark' });
</script>
