---
title: "UAT Feedback Portal"
permalink: /samples/uat-feedback-portal/
author_profile: false
toc: true
---

**A self-contained feedback and onboarding tool for a CMS ↔ Git sync workflow, built as an interactive Claude Artifact — no backend, no database, no deployment pipeline of its own.**

---

## Overview

Our documentation team was rolling out a new bidirectional sync between our `docops` Git repository and the Contentstack CMS: writers create docs in Git as before, and the sync pipeline automatically promotes them through a Sandbox CMS stack and on to Production. It's a meaningful shift in how the team works day to day, and like any workflow change, it needed real testing by the people who'd actually use it before it could be trusted.

That testing phase (UAT) surfaced a familiar problem: the *sync pipeline* had a clear rollout plan, but the *feedback loop* for testing it didn't. I designed and built the CMS ↔ Git Sync UAT Feedback Portal to close that gap — a single tool where writers log what they find, a reviewer can triage and respond in place, and new writers can self-serve their way through the workflow instead of shadowing someone.

![Dashboard view of the Feedback Portal, showing KPI cards for Open/Critical/Resolved/Awaiting Reply issues above a filterable table of submitted feedback]({{ site.baseurl }}/assets/images/uat-portal/dashboard.png)

---

## The problem

Before this tool existed, UAT feedback had nowhere structured to live:

- **Feedback was scattered.** A writer hitting a bug during testing would mention it in Slack, or tell someone verbally, or just remember to bring it up later. None of that was searchable, prioritized, or attributable.
- **No shared visibility.** As the person reviewing feedback, I had no single view of what had been reported, how severe it was, or who was supposed to be acting on it.
- **Closing the loop meant leaving the tool.** If I had a follow-up question on someone's report, the only option was to go DM them on Slack separately — which meant the question, and eventually the answer, lived somewhere the original report didn't.
- **Onboarding was tribal knowledge.** A new writer joining the sync workflow had to be walked through it by someone who already knew it, because the "documentation" for the workflow was really just accumulated experience.

## Objective

Build one lightweight, self-contained tool that:

1. Gives writers a fast, structured way to log UAT feedback — bugs, validation failures, suggestions — the moment they notice them.
2. Gives the reviewer at-a-glance visibility into severity, status, and ownership across everything reported.
3. Keeps the conversation about an issue attached to that issue, including the ability to loop in the right person by name and have them actually get notified.
4. Doubles as a living, testable guide to the sync workflow itself, so onboarding a new writer doesn't require a 1:1 walkthrough.

---

## What it does

### Structured feedback capture

Every submission captures who reported it, their contact email, which part of the workflow it touches, the issue type and severity, expected vs. actual behavior, and an optional screenshot. Issues get a readable auto-generated ID based on their type (`VAL-001`, `BUG-002`, `UX-003`), so they're easy to reference in conversation.

### Triage at a glance

KPI cards surface Open, Critical, Resolved, and "Awaiting Reply" counts the moment the portal loads, alongside a searchable, filterable, sortable table. Status and assignee are editable inline, and the whole dataset exports to CSV for offline reporting.

![The New Feedback form, with fields for Reporter Name, Reporter Email, Workflow Area, Issue Type, and Severity]({{ site.baseurl }}/assets/images/uat-portal/new-feedback-form.png)

### Conversation, not just tracking

This was the biggest gap the original version didn't solve. Every issue now has its own comment thread — viewable both in the full issue detail and in a lightweight, dedicated comments view — so a question like "can you clarify what you mean by this?" lives right next to the report it's about, instead of in a Slack DM that scrolls away. An "Awaiting Reporter Reply" flag sets and clears itself automatically as the conversation goes back and forth, so nothing silently stalls.

Typing `@` in a comment brings up a picker of teammates; selecting someone sends them a direct Slack message with a link straight back to that issue and comment. Getting this right meant catching a real bug before it shipped: two people at the company share the first name "Dhaval," and two share "Aravindh" — a naive name lookup could have silently notified the wrong person. Mentions now resolve against a small, human-verified directory instead of a fuzzy search.

![The dedicated Comments view for an issue, showing a threaded conversation with avatar initials and an @mention composer]({{ site.baseurl }}/assets/images/uat-portal/comments-view.png)

### A built-in team handoff guide

The portal's second tab is a full walkthrough of the actual sync architecture — a labeled diagram of how content flows from Git to Sandbox to Production, a numbered step-by-step guide with copy-paste-ready prompts for using Claude at each step, plus best practices, troubleshooting, and support info. It's meant to be the thing a new writer reads instead of being walked through the process by someone else.

![The Team Handoff Guide tab: the Git → Sandbox → Production architecture diagram, verification steps, best practices, troubleshooting, and support sections]({{ site.baseurl }}/assets/images/uat-portal/handoff-guide.png)

---

## How it helps the team

- **The reviewer gets one dashboard** instead of piecing together scattered Slack threads and hallway conversations.
- **Writers get a low-friction way to log something the moment they notice it**, without breaking their testing flow to open a ticketing tool.
- **Nothing falls through the cracks** — the awaiting-reply flag surfaces stalled conversations, and every comment is a permanent, searchable record instead of a message that scrolls out of view.
- **New writers onboard against a living reference**, not a static doc that quietly goes stale the first time the workflow changes.

## How it's built

The portal is a single self-contained HTML/CSS/JS Claude Artifact — no server to run, no database to provision. Issue and comment data persists in the browser; CSV export and the Slack @mention notifications both run through the Artifact platform's own capabilities rather than any custom backend infrastructure.

It was shaped almost entirely through real use: several rounds of "this behaves oddly here" and "can we also..." from actual testing drove the feature set, including catching that same-name notification bug before it could send a message to the wrong person, and a sandboxed-iframe quirk that had silently broken CSV export in the deployed version while appearing to work fine in local testing.

## Status

In active use for the CMS ↔ Git sync UAT effort, and continuing to evolve alongside the pipeline itself.
