---
title: "UAT Feedback Portal"
permalink: /samples/uat-feedback-portal/
author_profile: false
toc: true
---

**A self-contained feedback and onboarding tool for a CMS ↔ Git sync workflow, built as an interactive Claude Artifact — no backend, no database, no deployment pipeline of its own.**

## The problem

Our documentation team was rolling out a bidirectional sync between our `docops` Git repository and the Contentstack CMS, and UAT testing had no structured place to live: feedback was scattered across Slack and verbal mentions, I had no single view of what was reported or how severe it was, follow-up questions meant leaving the tool entirely, and onboarding a new writer meant walking them through it in person.

## What I built

The CMS ↔ Git Sync UAT Feedback Portal — one tool where writers log issues, I triage and respond in place, and new writers can self-serve through the workflow instead of shadowing someone.

![Dashboard view of the Feedback Portal, showing KPI cards for Open/Critical/Resolved/Awaiting Reply issues above a filterable table of submitted feedback]({{ site.baseurl }}/assets/images/uat-portal/dashboard.png)

KPI cards surface Open, Critical, Resolved, and "Awaiting Reply" counts at a glance, backed by a searchable, filterable, sortable table with inline status/assignee edits and CSV export. Each submission gets a readable auto-generated ID by type (`VAL-001`, `BUG-002`, `UX-003`).

![The New Feedback form, with fields for Reporter Name, Reporter Email, Workflow Area, Issue Type, and Severity]({{ site.baseurl }}/assets/images/uat-portal/new-feedback-form.png)

### Conversation, not just tracking

Every issue has its own comment thread, so follow-up questions live next to the report instead of scattering into Slack DMs. An "Awaiting Reporter Reply" flag sets and clears itself as the conversation moves. Typing `@` brings up a teammate picker that sends a direct Slack message linking back to the issue — resolved against a human-verified directory, since a naive name lookup could've notified the wrong person when two teammates share a first name.

![The dedicated Comments view for an issue, showing a threaded conversation with avatar initials and an @mention composer]({{ site.baseurl }}/assets/images/uat-portal/comments-view.png)

### Team handoff guide

A second tab walks new writers through the actual sync architecture — a labeled Git → Sandbox → Production diagram, step-by-step prompts for using Claude at each stage, and troubleshooting/support info — so onboarding doesn't require a 1:1 walkthrough.

![The Team Handoff Guide tab: the Git → Sandbox → Production architecture diagram, verification steps, best practices, troubleshooting, and support sections]({{ site.baseurl }}/assets/images/uat-portal/handoff-guide.png)

## How it's built

A single self-contained HTML/CSS/JS Claude Artifact — no server, no database. CSV export and Slack notifications run through the Artifact platform itself. Shaped almost entirely by real use during testing, including catching the same-name notification bug and a sandboxed-iframe quirk that had silently broken CSV export in production while working fine locally.

## Status

In active use for the CMS ↔ Git sync UAT effort, and evolving alongside the pipeline.
