---
title: "IA Monthly Goals Dashboard"
permalink: /samples/ia-goals-dashboard/
author_profile: false
toc: true
---

*An AI-assisted planning system for documentation team leads*

## Overview

Documentation team leads are expected to set meaningful monthly and quarterly goals for every writer on their team — goals that account for each person's project history, current capacity, and the team's push toward AI adoption, while still leaving room for documentation quality work that has nothing to do with AI at all. Done properly, this planning exercise touches nine or more people, several quarters of history, and a half-dozen categories of work. Done manually, it usually means digging through old trackers and meeting notes and reconstructing the same context every single month.

The IA Monthly Goals Dashboard is a lightweight internal tool built to remove that overhead. It centralizes a documentation team's goals — past, present, and future — into a single filterable view, and uses AI to help draft new goals on demand rather than requiring a lead to write every one by hand.

## What It Does

The dashboard tracks goals per writer across time, with each goal carrying a type (Primary or Secondary), a category (AI Adoption, Documentation Quality, Automation, and so on), a T-shirt size, and a status (To-do, In-progress, Done). Anyone using it can filter the goal list by month, quarter, or year, by writer, by category, by status, or by an Agent filter that isolates every goal representing an AI agent the team is actually building — surfacing the team's full agent-building backlog in one view, separate from ordinary documentation deliverables. Any goal flagged this way is also marked inline with an "AGENT:" label, so it reads as an agent commitment at a glance rather than just another line item.

Three features push it past a static tracker:

1. **An inline goal generator** — pick a month, optionally jot down a focus area or theme, and the dashboard calls Claude directly to draft a fresh Primary and Secondary goal for every writer on the team, tailored to their POD and grounded in a researched briefing on current SaaS documentation standards (Stripe- and Twilio-style API doc conventions, OpenAPI as the reference-doc norm) and where AI is moving in the documentation space (agents consuming docs via MCP, real-time content sync, multi-agent review workflows). To avoid accidentally clobbering a month that's already planned, the generate action is disabled by default for any month that already has goals — a lead has to explicitly opt in via a "regenerate" checkbox to overwrite it.
2. **Direct editability** — status, T-shirt size, and even the goal text itself can be changed inline.
3. **A lightweight "+ Add Goal" form** that lets a lead attach a third, fourth, or fifth goal to any writer in any period, rather than being boxed into a fixed two-goals-per-person structure.

A pair of charts at the bottom — goals by category and goals by writer — give an at-a-glance read on whether work is distributed evenly across the team and across focus areas, which is the kind of balance check that's easy to intend and easy to lose track of by the third month in.

## Architecture

The dashboard is a single self-contained HTML document, deployed as a persisted artifact rather than a one-off chat response — it lives in its own view, keeps working after the conversation that created it ends, and reopens with whatever state was last saved.

Underneath, the data model is a flat array of goal objects (writer, pod, type, category, size, status, agent, month, quarter, year), assembled from three layers at render time:

- **Base data** — a hand-curated seed set reconstructed from the team's actual planning documents (a Google Sheet tracker and a goals brief), giving the dashboard real historical grounding for Q1 and Q2 rather than starting from a blank slate.
- **Generated goals** — output from the in-dashboard AI generator, stored separately by month so a re-generation only touches the month it targets.
- **Manual additions** — anything added through the "+ Add Goal" form.

All three layers are merged, then any per-goal edits (status changes, resizes, rewritten goal text) are applied as an overlay keyed by goal ID. Everything is written to localStorage, so edits and generated content persist across reloads without needing a backend or database.

Quarters follow the team's actual fiscal calendar (Q1 Feb–Apr, Q2 May–Jul, Q3 Aug–Oct, Q4 Nov–Jan) rather than the calendar-year default, computed once from each goal's month and cached on the object.

The AI generation step calls `window.cowork.askClaude()` — a bridge exposed inside Cowork artifacts that runs live Claude inference from within the page — with a prompt constraining the model to a fixed JSON shape and an approved category list, so the response can be parsed directly into goal objects without manual cleanup. Charts are rendered with Chart.js; everything else is vanilla JavaScript, deliberately avoiding a framework given the size of the tool.

One deliberate limitation worth naming: the artifact's sandbox has no general internet access, only a small CDN allowlist, so the generator can't look up current trends or competitor documentation standards live. Instead, that research is done outside the tool — via a web search pass — and the findings are baked into the prompt as a dated "market context" briefing, surfaced in the UI so a lead can see exactly what's informing the suggestions and when it was last refreshed. It's a static snapshot rather than a live feed, and it needs a manual refresh pass periodically to stay current.

## Design

Visual design follows the company's internal brand system rather than a chart or component library default: a light, card-based layout with a purple/blue/green accent palette applied consistently across category tags, status pills, size badges, and chart series, so that a glance at any part of the page reads as the same product rather than a stack of default widgets bolted together.

## Impact

The underlying goal this tool was built against — set by the team's own planning brief — was to take monthly goal-setting from a multi-hour research exercise down to something a lead can do in a handful of minutes. The dashboard moves toward that by doing three things a spreadsheet can't: it holds the full history so nothing has to be reconstructed from scratch each month, it drafts the first pass of new goals instead of leaving that to a blank page, and it makes team-wide balance (who's carrying AI work, whether categories are lopsided, what's stuck in To-do) visible without cross-referencing anything by hand.

It also created an unplanned second benefit: because every AI-agent idea a writer proposes is tagged and filterable, the dashboard doubles as a running inventory of every agent the team is building or has built — turning what used to be scattered mentions across meeting notes into a single list a lead can point to.

## Tech Stack

Vanilla JavaScript and HTML/CSS · Chart.js for visualization · Claude (via the Cowork artifact bridge) for on-demand goal drafting · browser localStorage for persistence · Google Sheets/Docs as the source-of-truth for historical data.
