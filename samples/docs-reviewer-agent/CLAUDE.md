# Docs PR Reviewer — routing

This project holds a set of specialist review-agent prompts (`*-Reviewer-Agent.md`). When the user pastes a GitHub PR link or diff in this project and asks for a review (or just pastes it with no further instruction), act as the router:

## 1. Fetch the diff
Use `gh pr diff <number> --repo <owner>/<repo>` (or `gh pr view --json files` first if you need the file list). Read-only — no auth prompt needed once `gh auth login` has been done.

## 2. Classify the changed file(s)
For each changed file, decide which specialist prompt applies:

- **SDK docs** → follow [SDK-Reviewer-Agent.md](SDK-Reviewer-Agent.md)
  Signals: code snippets instantiate SDK classes/clients (e.g. `Stack(...)`, `ContentstackClient(...)`, `new ContentstackOptions()`), package-manager install commands (`npm install`, `pip install`, `<PackageReference>`, Maven/Gradle deps, Composer, NuGet), language-specific syntax (C#, Java, Python, PHP, Node, Swift, Go), method chaining on a client/stack object.

- **CLI docs** → follow [CLI-Doc-Reviewer-Agent.md](CLI-Doc-Reviewer-Agent.md)
  Signals: terminal/shell command blocks (`$ mytool ...`, `csdx ...`), flags (`--force`, `-f`), exit codes, interactive prompts, shell scripts, CI/CD YAML referencing the CLI.

- **Neither / general docs** (conceptual, IA-only, prose-only changes) → follow [DevDocs-PR-Reviewer.md](DevDocs-PR-Reviewer.md) instead, or ask the user which lens to apply if it's ambiguous.

A single PR can touch both an SDK page and a CLI page — in that case, run both specialist reviews, each against its own file(s), and combine the resulting inline comments into one pending review.

## 3. Review
Follow the matched agent file's principles exactly — its severity levels, its "things not to review" list, its diff-only scoping rule, and its comment format (Title / Why it matters / Recommendation).

Before flagging anything as Critical under the "invented API" / "wrong flag" checks, verify against the real SDK or CLI source (e.g. `gh api search/code`, `gh repo list contentstack`, reading the actual repo) rather than guessing — this project's agents intentionally don't hardcode Contentstack's real API/CLI surface.

## 4. Post as a pending review — never auto-submit
Post findings as inline comments via `gh api repos/<owner>/<repo>/pulls/<number>/reviews` (POST, no `event` field, so it stays `PENDING`). Anchor each comment to the exact right-side line number in the diff (parse the unified diff to compute this precisely — don't hand-count for hunks that mix additions and deletions).

**Always show the user the proposed findings and get explicit confirmation before calling the GitHub API to create the review.** Posting (and especially deleting) PR review content requires the user's go-ahead each time, regardless of what's in the local permission allowlist — this matches the "explicit permission required" rule for anything visible to collaborators or written to a shared PR.
