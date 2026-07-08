---
name: contentstack-changelog
description: Write Contentstack changelogs and release notes — both the post body and the full structured release-note artifact (post title, release date, post type, widget description, and product/plan/persona/channel tags). Use this whenever the user wants to announce a shipped change: a new feature, enhancement, bug fix, deprecation, or breaking change. Trigger on "changelog", "release note", "what's new", "announce this feature", "draft a release post", or any request to turn a shipped change into user-facing announcement copy, even if they don't say the word "changelog".
---

# Contentstack Changelog Writer

## Goal

Write changelogs and release notes that are **clear, concise, scannable, and user-focused**, and deliver them in Contentstack's full release-note format. A reader should quickly understand:

* What changed
* Why it matters
* How it affects them
* Any actions, defaults, or limitations

Release notes are **summaries**, not full product documentation. Produce both the prose *and* the surrounding metadata — a body with no filled-in fields is an incomplete deliverable.

---

## Output Format

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

---

## Core Writing Principles

### 1. Lead with User Impact

Start with **what changed and why users care**.

* Explain the benefit first.
* Keep introductions short (1–2 sentences).

**Preferred**

> We introduced a centralized Notifications section in Account Settings, giving you more control over how and where you receive notifications.

**Avoid** long intros, repeating the same idea, or implementation-first explanations.

**Bad**

> Instead of receiving everything by default, users can now configure notification behavior...

### 2. Optimize for Scannability

Users skim changelogs.

* Use bullets for multiple capabilities.
* Keep bullet structure parallel.
* Start bullets with action-oriented verbs.

**Preferred pattern**

* **Manage** notification channels globally
* **Customize** notifications by event
* **Choose** Analytics digest frequency

**Avoid** dense paragraphs, mixed sentence structures, and inconsistent bullet formatting.

### 3. Prioritize Outcomes Over Mechanics

Describe **what users can do**, not internal logic.

**Preferred**

> Turn email notifications off for all products at once.

**Only include mechanics when behavior matters**

> Global settings override individual event preferences.

**Avoid** technical implementation details, backend terminology, and system behavior users don't need to understand.

### 4. Keep It Concise

* Remove redundant phrases.
* Keep explanations short.
* Avoid unnecessary qualifiers.

**Prefer**

> Receive a bundled Analytics summary daily, weekly, or monthly.

**Avoid**

> Receive a single bundled summary daily, weekly, or monthly instead of receiving multiple notifications for every event.

### 5. Use Consistent Product Terminology

Match UI labels exactly, and **bold the exact UI label on first mention** so readers can trace it back to the interface (e.g., **Notifications**, **Account Settings**, **Email notifications**).

Keep terminology consistent throughout. Don't switch between settings / preferences / controls, or alerts / notifications / messages, unless the difference is intentional.

### 6. Highlight Exceptions and Defaults

Call attention to behaviors users should not miss: default-enabled functionality, non-configurable settings, exceptions to normal behavior, and usage limits or warnings.

**Preferred**

> **Note:** Critical alerts remain enabled by default.

Place it as a note, a separate paragraph, or a clearly visible callout.

### 7. Maintain Neutral Product Tone

Write like product communication, not marketing. Aim for clear, professional, direct, and user-oriented. Avoid "exciting", "powerful", "game-changing", "exactly what you need", "seamless experience".

### 8. End with Next Steps

Point users to more detail without overloading the note.

**Preferred**

> Check the documentation for full details or contact support.

---

## Voice and Tense

Keep these consistent across every release note:

* **Second person** for the reader: "you", "your **Account Settings**".
* **"We"** for the announcement of what shipped: "We introduced…".
* **Present-perfect or simple past** for the announcement itself ("We've introduced", "We added").
* **Present tense** for what users can now do ("you can now manage…", "global settings override…").

---

## Link Conventions

* Use **descriptive anchor text** that names the destination. Link the word "documentation" or the topic — never "click here" or a bare URL.
* Standard closing pattern: one link to the relevant docs page, plus a support contact.

**Preferred**

> Check our [documentation](…) for full details, or contact [support](mailto:…) for more information.

---

## Post Type Guidance

The framing changes by type. Pick the type first, then write to its pattern.

* **New feature** — Lead with the capability and benefit. Follow the standard structure below.
* **Enhancement** — Anchor to the existing feature, then state what improved and the resulting benefit. Keep it tighter than a new-feature note.
* **Bug fix** — State what was wrong and that it's resolved, in plain terms. One or two sentences is usually enough. Skip bullets unless several fixes ship together.
* **Deprecation** — Lead with **what is going away and when**. State the replacement and the migration path up front, and flag any action the user must take before the deprecation date. Use a clearly visible note for the date.
* **Breaking change** — Lead with **action required**. State exactly what breaks, who is affected, the timeline, and the migration steps before describing anything else. Never bury a breaking change inside a feature announcement.

---

## Recommended Body Structure

1. **Short intro** — what changed and why it matters (1–2 sentences).
2. **Bulleted capabilities** — action-oriented, parallel structure.
3. **Important notes** — defaults, exceptions, critical alerts.
4. **Learn more** — docs and support.

### Title rules

Keep titles clear and feature-specific.

**Good**

* Manage Notification Preferences in Account Settings
* Centralized Notification Preferences

**Avoid** vague titles, marketing headlines, and overly long titles.

### Widget Description rules

The dashboard blurb is its own deliverable, not a trimmed title.

* One sentence, **under 160 characters**.
* Lead with the capability; make it stand alone without the body.
* No marketing tone.

**Example**

> Manage in-app and email notifications by product and event from a new Notifications section in Account Settings.

---

## Worked Example

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

## Post type
New feature

## Product(s)
[x] Administration

## Plan(s)
[x] Start   [x] Grow   [x] Scale   [x] Add-on

## User role(s)/persona(s)
[x] Admin

## Publication channel(s)
[x] Documentation Site | Changelog   [x] In-app | What's New? Modal
```

---

## Review Checklist

Before publishing, validate:

* [ ] All metadata fields filled (or marked `[needs confirmation]`)
* [ ] Release date in `MMM D, YYYY` format
* [ ] Post type chosen and body matches its pattern
* [ ] Widget Description present and under 160 characters
* [ ] Product / Plan / Persona / Channel tags reflect who's actually affected
* [ ] Title clearly communicates the feature
* [ ] Intro explains user impact quickly
* [ ] Content is concise and skimmable
* [ ] Bullets follow parallel structure, action-first
* [ ] User outcomes prioritized over mechanics
* [ ] UI labels bolded and matched exactly to the product
* [ ] Voice/tense consistent (you / we / present tense)
* [ ] Exceptions and defaults clearly highlighted
* [ ] Descriptive link text; docs + support path included
* [ ] Neutral, professional tone — no marketing phrasing
* [ ] Reads like a changelog, not product documentation

---

## Anti-Patterns

* **Over-explaining** — "Instead of receiving everything by default, users can now configure how notifications work across products and communication channels." → "Manage notifications by product and channel from one place."
* **Documentation-heavy writing** — Summarize capabilities; don't explain every workflow.
* **Repetition** — Don't restate the same benefit multiple times.
* **Marketing tone** — No promotional phrasing disguised as release notes.
* **Body-only delivery** — Don't hand over a post body with the metadata fields left blank.
