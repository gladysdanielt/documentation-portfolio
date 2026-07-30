---
title: "Changelog Writing Skill (SKILL.md)"
permalink: /samples/skill-changelog-viewer/
author_profile: false
toc: true
---

## Purpose

This is a **SKILL.md file** — a structured, reusable prompt that an AI assistant (like Claude) loads before drafting a Contentstack changelog or release note. Instead of re-explaining the team's writing standards every time, the assistant reads this file and already knows the required structure, tone, metadata fields, and common mistakes to avoid.

It's version-controlled, reviewable, and shared — so "how we write changelogs" lives in one place instead of in individual writers' heads.

## What It Achieves for the Technical Writing Team

- **Consistency at scale** — every writer, junior or senior, gets AI-assisted drafts that already match house style, structure, and tone instead of everyone improvising their own format.
- **Complete drafts, not partial ones** — the skill enforces that metadata (post title, release date, post type, widget description, product/plan/persona/channel tags) ships alongside the prose, not as a follow-up task.
- **Fewer review cycles** — editors spend review time on accuracy and clarity, not on re-teaching format and voice on every release note.
- **Built-in QA** — a review checklist and a named list of anti-patterns act as a self-check the assistant runs before handing off a draft, catching missing metadata or marketing-toned language before a human ever sees it.
- **Faster onboarding** — a new or junior writer can produce a compliant release note from day one, because the style guide is encoded directly into the tool they're already using.

## How It's Structured

This article describes the skill's shape without reproducing the actual prompt text.

- **A required output shape.** Every release note ships with its full metadata — title, release date, post type, an audience-tagging block (product, plan, persona, publication channel), and a character-capped dashboard blurb — alongside the prose, not as a follow-up task. Fields the assistant isn't confident about get flagged for confirmation rather than guessed.
- **A set of writing principles**, covering things like leading with user impact before mechanics, writing for scannability, keeping terminology and UI-label casing consistent, and calling out defaults/exceptions users shouldn't miss.
- **Type-specific framing.** A new feature, an enhancement, a bug fix, a deprecation, and a breaking change each get a different structural pattern — a breaking change leads with what action is required, for instance, where a bug fix just states what was wrong and that it's resolved.
- **A worked example** the model pattern-matches against, showing all fields filled in for a realistic feature announcement.
- **A review checklist and a named list of anti-patterns** (things like over-explaining, marketing tone, or shipping prose with the metadata left blank) that the assistant checks itself against before handing off a draft.

The result is closer to a style guide the tool actually enforces than one someone has to remember to consult.
