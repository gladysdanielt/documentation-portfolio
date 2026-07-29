---
title: "Building the IA Agents Tracker"
permalink: /samples/ia-agents-tracker/
author_profile: false
toc: true
---

*A living dashboard for team-wide AI adoption*

## The Problem

Documentation teams don't have a shortage of AI ideas — they have a shortage of *visibility* into them. On our team, writers were independently building AI agents to solve real problems in their PODs: a Slack scraper to surface documentation gaps, a chatbot to validate a Common Issues Library, a script to auto-flag stale in-product prompts. Each of these lived in someone's head, a Slack thread, or a half-finished doc. There was no shared place to see what agents existed, who owned them, what state they were in, or whether someone else on the team had already solved the same problem.

At the same time, our monthly IA goal-setting process already had a home — a Goals Dashboard that tracked writer assignments by category, status, and time period. What was missing was the equivalent view for the *agents themselves*: the actual AI tools being built, not just the goal to build them.

## What It Is

The IA Agents Tracker is a companion dashboard to the IA Monthly Goals Dashboard, built as a self-contained, single-page tool that lives inside Cowork and refreshes on demand. It's organized into three tabs, each solving a distinct piece of the same underlying problem: **knowing what AI work is happening, how to do it well, and how to reuse what's already been built.**

### 1. Agents

The core tracker. Any writer can log any number of agents they're building or maintaining — no fixed limit, no gatekeeping. Each entry captures a name, a description, the month and fiscal quarter it belongs to, and a status of **To-do**, **In-progress**, or **Done**.

Two decisions shape how this tab behaves day to day:

- **Category is inferred, not chosen.** Rather than asking writers to self-classify (which tends to drift inconsistent across a team), the dashboard runs a keyword-scoring engine against the agent's name and description and assigns it to one of eleven categories — AI Adoption, Automation, Knowledge Management, Content Health, and so on — the same taxonomy already used in the Goals Dashboard. Writers can still override the guess if it's off, but the default path requires zero extra decisions.
- **You can't mark something Done without proof.** Every agent has a Link field, and the status dropdown physically refuses to move to Done until that field has something in it. This was a direct response to a familiar failure mode: status trackers that say "Done" but point to nothing. A small popup blocks the change and asks for a link before it'll let you proceed.

Filters (Period, Writer, Category, Status, free-text search) mirror the Goals Dashboard so the two tools feel like one system, and three charts — by category, by status, by writer — sit at the bottom for an at-a-glance read of where the team's AI effort is concentrated.

### 2. Agent Creation Guidelines

A living checklist, not a static PDF. Our team had already written down a set of practices for building agents responsibly — things like using company SSO rather than personal accounts, naming conventions, keeping prompts conversational, assigning a single owner per agent, and retiring agents that no longer earn their keep. Putting these in a PDF would have made them easy to write and easy to ignore.

Instead, the guidelines live in the same dashboard, organized into ten categories, filterable and keyword-searchable. Anyone can add a new guideline as the team learns something new, and anyone can retire one that's gone stale — both actions gated behind a confirmation step.

### 3. Prompt Library

The newest addition, aimed at the most common form of duplicated effort: writers independently reinventing the same prompt. This tab lets anyone save a named, categorized prompt (DocsWork, IA, Research, Competitor Analysis) with the full prompt text, and every entry automatically tracks when it was created and last updated.

The interesting design constraint here was ownership without real authentication. The dashboard has no login system, so instead each writer selects "who they are" once from a dropdown, and that identity is remembered locally. A prompt is only editable or deletable by the person who added it — everyone else sees it in read-only form. It's a lightweight trust model, not a security boundary, but it's enough to stop accidental overwrites in a small, cooperative team.

## Architecture

The whole thing is a single self-contained HTML file — no server, no build step, no database. That constraint was deliberate:

- **Rendering** — plain HTML/CSS/JS, styled to match the team's existing brand palette and the visual language of the Goals Dashboard, so the two tools read as one product rather than two disconnected experiments.
- **Charts** — Chart.js, loaded from CDN, for the category/status/writer breakdowns.
- **State** — everything (agents, guidelines, prompts, overrides, the current "identity," even which tab you last had open) lives in the browser's localStorage. There's a base seed dataset (agents already known from the team's existing tracker) plus a manual layer for anything added through the UI; edits are stored as field-level overrides rather than mutating the seed data directly, which makes it possible to reset or audit changes later.
- **Interaction pattern** — inline editing throughout (click a pencil icon, edit in place, save or cancel), rather than separate edit screens. Destructive actions — deleting an agent, a guideline, or a prompt — all route through one shared confirmation modal that requires typing the word "DELETE" before the button even becomes clickable.
- **No invented data** — where real data existed (agent names, statuses, months), it was pulled from the team's actual tracker. Where it didn't exist (links, prompt content), fields were left empty rather than filled with placeholder text that could be mistaken for real information.

## Impact

This shipped recently, so team-wide usage data doesn't exist yet — any specific adoption number here would be a guess, and guesses don't belong in a portfolio piece. What can be said with confidence is what the tool is designed to change:

- **Visibility replaces memory.** Nine writers' worth of AI agent work, previously scattered across Slack and individual context, now lives in one filterable view.
- **A link requirement turns "Done" into a checkable claim** instead of a self-report, which matters more as more agents accumulate and no one person can hold the full picture in their head.
- **Guidelines that live where the work happens** are more likely to get followed — and more likely to get improved, since adding one is as easy as filling in a text box.
- **A shared prompt library reduces the single most avoidable form of duplicate work** on an AI-adopting team: two people solving the same problem with two different prompts because neither knew the other had already done it.

The next real test is adoption — whether writers actually populate it during the normal cadence of monthly IA planning, or whether it needs another iteration to fit naturally into that workflow. That's the honest next chapter, not a number to report yet.
