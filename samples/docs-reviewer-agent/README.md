# Docs Reviewer Agent

A set of Claude Code agent prompts that review documentation PRs the way a senior technical writing lead would — focused, high-value comments only, never a rewrite. [CLAUDE.md](CLAUDE.md) wires them together so you can just paste a PR and get a review.

## What this agent can do

- **Fetch a PR's diff** from GitHub via `gh` (read-only — `gh pr view`, `gh pr diff`, `gh api` GET calls run without a prompt once you're authenticated).
- **Classify the changed docs** as SDK reference, CLI reference, or general/conceptual content, and route to the matching specialist reviewer below.
- **Review only the changed lines** — unchanged content on the page is read for context (e.g., "was auth already shown earlier?") but is never itself flagged.
- **Verify claims against the real source** before flagging something Critical — e.g. pulling the actual SDK repo (`gh api search/code`, reading real class/method signatures) or the CLI's actual help output/flags, rather than guessing.
- **Post findings as inline PR comments**, anchored to the exact right-side line in the diff, formatted as Title / Why it matters / Recommendation.
- **Leave the review as a pending/draft review — never auto-submit.** You check the comments on GitHub, delete anything irrelevant, and submit yourself.
- **Delete a pending review** on request (e.g., if you want to redo it).
- Always asks for explicit confirmation before actually posting or deleting anything on GitHub, regardless of local permission settings — those actions are visible to collaborators or destructive, so they're never silent.

## Sub-agents

| Agent | File | Fires on | Focus |
|---|---|---|---|
| **SDK Reviewer** | [SDK-Reviewer-Agent.md](SDK-Reviewer-Agent.md) | SDK reference/how-to pages (install → init → auth → method calls in a given language) | Onboarding completeness, language idioms (npm/pip/Maven/NuGet/Composer conventions), missing imports/error handling/pagination/cleanup, response handling, invented/incorrect API names (highest-priority check), version consistency, multi-language parity, sample data realism. Verifies against `https://www.contentstack.com/docs/developers/sdks` and real SDK source. |
| **CLI Doc Reviewer** | [CLI-Doc-Reviewer-Agent.md](CLI-Doc-Reviewer-Agent.md) | CLI reference/how-to pages (commands, flags, terminal output) | Developer journey ordering (overview → prereqs → install → auth → quick start → reference → output → examples → troubleshooting), Quick Start minimality, flag/default/alias documentation, example redundancy, output/exit-code documentation, interactive-mode and automation correctness (a high-priority area — checks for unsafe scripting, false success detection), troubleshooting structure (Problem → Cause → Resolution), information architecture. Capped at 8 comments. |
| **Dev Docs PR Reviewer** | [DevDocs-PR-Reviewer.md](DevDocs-PR-Reviewer.md) | General/conceptual docs that aren't SDK or CLI reference content (fallback) | Information architecture, developer experience, redundancy (its strongest category), scope/implementation leakage, example quality, troubleshooting, command-reference consistency, AI-generated-writing tells. |

All three share the same constraints: three severity levels only (Critical / Important / Nice to Have), never rewrite prose, ignore grammar/tone/Oxford commas/style nits, and only comment where it materially affects whether a developer can complete the task.

There's also an older draft, [CLI-Reviewer-Agent.md](CLI-Reviewer-Agent.md), kept for reference but not currently wired into the router — [CLI-Doc-Reviewer-Agent.md](CLI-Doc-Reviewer-Agent.md) is the active CLI agent.

## Usage

1. Run `gh auth login` once (HTTPS is simplest).
2. In this project, paste a PR link or diff and ask for a review.
3. The agent fetches the diff, classifies it, reviews it, and shows you the proposed inline comments.
4. Confirm to post — it creates a **pending** GitHub review (not visible to other collaborators, nothing submitted).
5. Check the draft on GitHub, delete anything irrelevant, and submit it yourself — or ask the agent to delete the pending review if you want to redo it.

## Other files

- [Docs-Review-Agent-Build-Plan.md](Docs-Review-Agent-Build-Plan.md) — a stretch-goal build plan for turning this into a standalone web app (paste-a-PR frontend + orchestrator backend + more specialist agents like API, Concept, Release Notes reviewers). Not built — this Claude Code project is the working version today.
- [.claude/settings.json](.claude/settings.json) — a permission allowlist for frequently-used **read-only** commands (e.g. `gh pr view`, Contentstack CDA reads, GitHub raw-file fetches) so those don't prompt. Anything that writes or deletes — including posting/deleting PR reviews — always asks first.
