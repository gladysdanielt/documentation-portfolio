# Build Plan: Docs PR Review Agent System

Goal: a web front end where you paste a PR (link or diff), click **Review**, and a set of specialist agents — orchestrated by one Lead agent — return GitHub-ready review comments, based on `DevDocs-PR-Reviewer.md`.

Note up front: I'm not certain which exact frameworks/hosting you'll want to use — the stack below is a reasonable default, not a fixed requirement. Verify current library/API syntax against official docs before implementing, since exact method names can change between versions.

---

## 1. Architecture

```
[Frontend: PR input + Review button]
        |
        v
[Backend service]
   1. Fetch PR diff + changed files (GitHub API)
   2. Call Lead Documentation Reviewer (orchestrator)
        - classifies doc type
        - decides which sub-agents to invoke
   3. Fan out to relevant sub-agents (parallel Claude API calls,
      one system prompt per agent)
   4. Aggregate: dedupe, merge severity, sort by priority
   5. Format as GitHub-review-comment blocks
        |
        v
[Frontend: renders comments; optional "Post to GitHub" action]
```

Three layers, each independently buildable:
- **Frontend** — input box (PR URL or pasted diff/markdown), Review button, results view.
- **Orchestration backend** — where the multi-agent logic lives. This is the core IP; the frontend is just a shell around it.
- **GitHub integration** — fetching the diff and (optionally) posting comments back. Can be stubbed early (manual paste-in) and added later.

### Suggested stack (default, not fixed)
- Frontend: a single-page app (React or plain HTML/JS) — simple enough that a minimal static page calling your backend is sufficient at first.
- Backend: Node.js or Python service that calls the Claude API (Messages API) for each agent, and the GitHub REST API (via Octokit for Node, or PyGithub/`requests` for Python) to pull PR diffs and post comments.
- Orchestration: rather than a heavier multi-agent framework, start with plain sequential/parallel API calls in your backend code — one call per agent with a distinct system prompt, then merge results in code. This is simpler to debug than an agentic framework and gives you full control over dedup/priority logic. You can migrate to Claude Agent SDK-style orchestration later if useful — verify current SDK APIs in Anthropic's docs before adopting it, since I don't have certainty on the exact current method names.

---

## 2. Agent inventory, mapped to your existing prompt

Your uploaded file already contains the content for most of these — it just needs to be split by section into separate system prompts.

| # | Agent | Source in DevDocs-PR-Reviewer.md |
|---|---|---|
| 1 | Lead Documentation Reviewer (orchestrator) | New — combines "Severity Levels," "Review Style," "Output Format," "Stretch Goal" sections + doc-type routing logic |
| 2 | Information Architecture Reviewer | Section 1 |
| 3 | Developer Experience Reviewer | Section 2 |
| 4 | Duplication Detector | Section 3 (Redundancy) |
| 5 | AI Writing Quality Reviewer | Section 8 |
| — | Scope Reviewer *(optional 6th core agent)* | Section 4 — worth keeping since it's distinct from IA and catches implementation leakage |
| — | Examples Reviewer | Section 5 |
| — | Troubleshooting Reviewer | Section 6 |
| — | CLI Reviewer | Section 7 (command-reference parts) — needs expansion for flags/exit codes/interactive mode |
| — | API Reviewer | Not yet in your file — new prompt needed |
| — | SDK Reviewer | Not yet in your file — new prompt needed |
| — | Concept Reviewer | Not yet in your file — new prompt needed |
| — | Release Notes Reviewer | Not yet in your file — new prompt needed |
| — | Style Guide Compliance Agent | Not yet in your file — needs your org's actual style guide as input |

All sub-agents keep the shared rules from your file: only 3 severity levels, the Title / Why it matters / Recommendation comment format, and the "don't rewrite, don't nitpick grammar" constraint. Put those shared rules in one reusable block so every agent prompt stays consistent.

---

## 3. Lead Documentation Reviewer — orchestrator logic

This agent doesn't write review comments itself. Its job, each run:

1. **Classify the doc**: conceptual, task/how-to, CLI reference, API reference, SDK reference, troubleshooting, or release notes.
2. **Select sub-agents** based on doc type — e.g., a CLI reference page triggers CLI Reviewer + IA + DX + Duplication + AI Quality, but skips Troubleshooting and Examples unless those sections are present in the diff.
3. **Invoke the selected sub-agents** with the PR diff (and enough surrounding context to judge structure, per the Stretch Goal in your file).
4. **Merge results**:
   - Drop duplicate findings (e.g., IA and Duplication both flagging the same repeated section — keep the more specific one).
   - Re-sort by severity (Critical → Important → Nice to Have).
   - Cap Nice to Have comments; cap total comments per your file's stretch goal (5–8 high-impact comments).
5. **Output** in the GitHub review comment format already specified in your file.

This classify → route → fan-out → merge pattern is the one piece of custom logic worth writing carefully; everything else is prompt engineering.

---

## 4. Build phases

**Phase 1 — Core 5 agents, no frontend yet**
Write the 5 system prompts (split from your file) and the orchestrator's routing/merge logic. Test by manually pasting a real PR diff into a script and checking output quality against past reviews you've actually left. This is the highest-leverage phase — get the prompts and merge logic right before building UI around them.

**Phase 2 — Minimal frontend**
Single page: paste PR URL or diff → Review button → rendered comment list. No GitHub write access yet.

**Phase 3 — GitHub integration**
Fetch the diff automatically from a PR URL (GitHub API, read-only first). Add "Post to GitHub" as a separate, explicit action — don't auto-post, since these are your professional review comments and you'll want to skim before publishing.

**Phase 4 — Domain-specific agents**
Add CLI, API, SDK, Concept, Troubleshooting, Release Notes reviewers one at a time, each gated by the orchestrator's doc-type classifier. Prioritize whichever doc type you review most often day-to-day.

**Phase 5 — Tuning**
Run it against 10–15 of your own past PR reviews and compare its output to what you actually wrote. Adjust prompts where it over-comments, misses known issues, or flags things you explicitly exclude (grammar, tone, Oxford commas per your file).

---

## 5. Open decisions before building

- **Auth for GitHub**: personal access token vs. a GitHub App — affects whether this stays a personal tool or something your whole docs team can use.
- **Hosting**: local script vs. a small deployed service — depends on whether this needs to be shared.
- **Model choice per agent**: all agents can use the same model, or lighter/cheaper models for narrower agents (e.g., Duplication Detector) and a stronger model for the Lead orchestrator's synthesis step.
- **Comment posting**: draft-only (you review and post manually) vs. direct posting to the PR — recommend draft-only initially given how much judgment is baked into "which comments are actually worth 5–8 slots."

---

## 6. Verification plan

Before treating this as production-ready:
- Run it against 3–5 already-merged PRs where you know what you *would have* commented, and compare.
- Check the orchestrator doesn't invoke irrelevant agents (e.g., CLI Reviewer firing on a conceptual doc).
- Check dedup actually collapses overlapping IA/Duplication findings rather than emitting both.
- Confirm output never exceeds the 5–8 comment cap and never includes grammar/tone nits, per your file's exclusion list.
