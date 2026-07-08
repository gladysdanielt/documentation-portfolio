---
title: "Doc Engineering"
permalink: /documentation-engineering/
toc: true
---

Documentation is a product, not just a set of pages. This section covers the systems and tooling work behind the Contentstack docs — analytics, structure, and automation.

## 🔌 MCP Server for the Docs Site

Built an MCP server that discovers docs via **llms.txt** and **sitemap.xml**, embeds them into a **vectorized database**, and exposes search as MCP tools — with a **scheduled Claude cron job** that keeps the index current as the CMS changes.

- [Read the full write-up]({{ site.baseurl }}/samples/mcp-server-project/)

## 🧩 SKILL.md Files for the Team

Created SKILL.md files for the team — reusable, structured prompts that encode writing standards so AI-assisted drafts come out consistent regardless of who runs them.

### Purpose

This is a **SKILL.md file** — a structured, reusable prompt that an AI assistant (like Claude) loads before drafting a Contentstack changelog or release note. Instead of re-explaining the team's writing standards every time, the assistant reads this file and already knows the required structure, tone, metadata fields, and common mistakes to avoid.

It's version-controlled, reviewable, and shared — so "how we write changelogs" lives in one place instead of in individual writers' heads.

### What It Achieves for the Technical Writing Team

- **Consistency at scale** — every writer, junior or senior, gets AI-assisted drafts that already match house style, structure, and tone instead of everyone improvising their own format.
- **Complete drafts, not partial ones** — the skill enforces that metadata (post title, release date, post type, widget description, product/plan/persona/channel tags) ships alongside the prose, not as a follow-up task.
- **Fewer review cycles** — editors spend review time on accuracy and clarity, not on re-teaching format and voice on every release note.
- **Built-in QA** — the Review Checklist and Anti-Patterns sections act as a self-check the assistant runs before handing off a draft, catching missing metadata or marketing-toned language before a human ever sees it.
- **Faster onboarding** — a new or junior writer can produce a compliant release note from day one, because the style guide is encoded directly into the tool they're already using.

### The Skill File

#### Goal

Write changelogs and release notes that are **clear, concise, scannable, and user-focused**, and deliver them in Contentstack's full release-note format. A reader should quickly understand:

- What changed
- Why it matters
- How it affects them
- Any actions, defaults, or limitations

Release notes are **summaries**, not full product documentation. Produce both the prose *and* the surrounding metadata — a body with no filled-in fields is an incomplete deliverable.

#### Output Format

ALWAYS produce all of these fields. Don't stop at the post body — the metadata is part of the artifact.

```markdown
## Post title
[Clear, feature-specific title — see Title rules]

## Release date
[MMM D, YYYY]   ← e.g., Jun 20, 2026

## Post type
[One of: New feature | Enhancement | Bug fix | Deprecation | Breaking change]

## Post body
[Short intro → bulleted capabilities → important notes → learn-more link]

## Widget Description (under 160 characters)
[One sentence for the stack dashboard — leads with the capability]

## Product(s)
[ ] CMS   [ ] Administration   [ ] Visual Builder   [ ] Personalize
[ ] AI    [ ] Automate   [ ] Launch   [ ] Marketplace
[ ] SDK   [ ] CLI   [ ] APIs

## Plan(s)
[ ] Start   [ ] Grow   [ ] Scale   [ ] Add-on

## User role(s)/persona(s)
[ ] Content Manager   [ ] Developer   [ ] Admin

## Publication channel(s)
[ ] Documentation Site | Changelog   [ ] In-app | What's New? Modal
```

If you don't have enough information to fill a field confidently (e.g., which plans are affected), mark it clearly as `[needs confirmation]` rather than guessing. Tagging the wrong plan or persona sends the announcement to the wrong audience.

#### Core Writing Principles

**1. Lead with User Impact** — start with what changed and why users care. Explain the benefit first; keep introductions to 1–2 sentences.

> Preferred: "We introduced a centralized Notifications section in Account Settings, giving you more control over how and where you receive notifications."

**2. Optimize for Scannability** — use bullets for multiple capabilities, keep bullet structure parallel, and start bullets with action-oriented verbs.

**3. Prioritize Outcomes Over Mechanics** — describe what users can do, not internal logic. Only include mechanics when behavior actually matters (e.g., "Global settings override individual event preferences").

**4. Keep It Concise** — remove redundant phrases and unnecessary qualifiers.

**5. Use Consistent Product Terminology** — match UI labels exactly, and bold the exact UI label on first mention. Don't switch between synonyms (settings/preferences/controls) unless the difference is intentional.

**6. Highlight Exceptions and Defaults** — call out default-enabled functionality, non-configurable settings, and usage limits as a clearly visible note.

**7. Maintain Neutral Product Tone** — write like product communication, not marketing. Avoid "exciting," "powerful," "game-changing," "seamless experience."

**8. End with Next Steps** — point to documentation or support without overloading the note.

#### Voice and Tense

- **Second person** for the reader: "you," "your **Account Settings**."
- **"We"** for the announcement of what shipped: "We introduced…"
- **Present-perfect or simple past** for the announcement itself: "We've introduced," "We added."
- **Present tense** for what users can now do: "you can now manage…"

#### Link Conventions

- Use **descriptive anchor text** that names the destination — never "click here" or a bare URL.
- Standard closing pattern: one link to the relevant docs page, plus a support contact.

#### Post Type Guidance

| Type | Framing |
|---|---|
| New feature | Lead with the capability and benefit; follow the standard structure |
| Enhancement | Anchor to the existing feature, then state what improved; keep it tighter |
| Bug fix | State what was wrong and that it's resolved, in plain terms — usually 1–2 sentences |
| Deprecation | Lead with what's going away and when; state the replacement and migration path up front |
| Breaking change | Lead with action required — what breaks, who's affected, timeline, migration steps |

#### Recommended Body Structure

1. **Short intro** — what changed and why it matters (1–2 sentences)
2. **Bulleted capabilities** — action-oriented, parallel structure
3. **Important notes** — defaults, exceptions, critical alerts
4. **Learn more** — docs and support

#### Worked Example

A complete release note, all fields filled:

```markdown
## Post title
New Notification Preferences in Account Settings

## Release date
Jun 20, 2026

## Post type
New feature

## Post body
We've introduced a centralized **Notifications** section in your **Account Settings**,
giving you more control over what you're notified about and where. You can now manage
notifications by product and event across both in-app and email channels from one place.

From the **Notifications** section, you can:

* **Set global channel controls**: Turn in-app or email notifications on or off for all
  products at once. Global settings override individual event preferences — for example,
  turning off email pauses all notification emails, including digests.
* **Tune notifications by event**: Enable or disable notifications for specific product
  events and choose how you receive them — in-app, email, or both, depending on what the
  product supports.
* **Choose your Analytics digest cadence**: Receive a single bundled Analytics summary
  daily, weekly, or monthly instead of separate emails for each event.
* **Stay aligned with your role**: Notifications reflect your organization roles and
  permissions, so you only receive notifications relevant to your access level.

**Note:** Critical alerts, such as Analytics over-usage and threshold warnings, remain
enabled by default so you're notified before your organization exceeds a usage limit.

Check our [documentation](https://www.contentstack.com/docs/...) for full details, or
contact [support](mailto:support@contentstack.com) for more information.

## Widget Description (under 160 characters)
Manage in-app and email notifications by product and event from a new Notifications
section in Account Settings.

## Product(s)
[x] Administration

## Plan(s)
[x] Start   [x] Grow   [x] Scale   [x] Add-on

## User role(s)/persona(s)
[x] Admin

## Publication channel(s)
[x] Documentation Site | Changelog   [x] In-app | What's New? Modal
```

#### Review Checklist

Before publishing, validate:

- [ ] All metadata fields filled (or marked `[needs confirmation]`)
- [ ] Release date in `MMM D, YYYY` format
- [ ] Post type chosen and body matches its pattern
- [ ] Widget Description present and under 160 characters
- [ ] Product / Plan / Persona / Channel tags reflect who's actually affected
- [ ] Title clearly communicates the feature
- [ ] Intro explains user impact quickly
- [ ] Bullets follow parallel structure, action-first
- [ ] UI labels bolded and matched exactly to the product
- [ ] Voice/tense consistent (you / we / present tense)
- [ ] Exceptions and defaults clearly highlighted
- [ ] Neutral, professional tone — no marketing phrasing

#### Anti-Patterns

- **Over-explaining** — "Instead of receiving everything by default, users can now configure how notifications work across products and communication channels." → "Manage notifications by product and channel from one place."
- **Documentation-heavy writing** — summarize capabilities; don't explain every workflow.
- **Repetition** — don't restate the same benefit multiple times.
- **Marketing tone** — no promotional phrasing disguised as release notes.
- **Body-only delivery** — don't hand over a post body with the metadata fields left blank.

## 🛠️ Troubleshooting Portal

Contributed to ideating the troubleshooting portal for Contentstack — templated its structure and pulled supporting information from the customer Salesforce database.

- [Troubleshooting sample page](https://www.contentstack.com/docs/headless-cms/installation-initialization-environments)

## 🎨 Rich Media

Complemented doc articles with explainer diagrams and architecture flows to make complex concepts easier to follow at a glance.

- [Explainer diagram — About Publish Rules](https://www.contentstack.com/docs/headless-cms/about-publish-rules)
- [Architecture flow — Simple Website with Contentstack Launch](https://www.contentstack.com/docs/headless-cms/simple-website-with-contentstack-launch)

## 🔄 End-to-End Doc Flow App

Collaborated on building automation for AI-assisted doc creation, including an app built with Google AI Studio to handle multiple stages of the documentation development lifecycle (DDLC) in one place.

- [Docs Ninja Agent](https://htmlpreview.github.io/?https://github.com/gladysdanielt/Docs-Ninja-Agent/blob/main/index.html)

## 📊 Docsite Analytics

Part of the team that runs docsite analytics using **Microsoft Clarity** and **Swiftype**, to understand how readers actually search and navigate the docs.

- [Sample Clarity Report]({{ site.baseurl }}/samples/clarity-report/) — viewable as tables directly in the browser

## 🖌️ Site Redesign

Contributed to the redesign of the [Contentstack documentation website](https://www.contentstack.com/docs).
