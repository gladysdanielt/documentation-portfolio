---
title: "Doc Engineering"
permalink: /documentation-engineering/
toc: true
---

Documentation is a product, not just a set of pages. This section covers the systems and tooling work behind the Contentstack docs — analytics, structure, and automation, including **retrieval-augmented generation (RAG)** for AI-powered doc search and **AI-driven orchestration** across the documentation lifecycle.

## 🔌 MCP Server for the Docs Site

Built an MCP server that discovers docs via **llms.txt** and **sitemap.xml**, embeds them into a **vectorized database**, and exposes search as MCP tools — with a **scheduled Claude cron job** that keeps the index current as the CMS changes.

- [Read the full write-up]({{ site.baseurl }}/samples/mcp-server-project/)

## 🔍 Multi-Agent Documentation Reviewer

Built a system of specialist Claude Code review agents (SDK, CLI, and general docs) that review documentation PRs, verify claims against real source code before flagging anything critical, and post findings as a pending — never auto-submitted — GitHub review.

- [Read the full write-up]({{ site.baseurl }}/samples/docs-reviewer-agent/article/)

## 🧩 SKILL.md Files for the Team

Created SKILL.md files for the team — reusable, structured prompts that encode writing standards so AI-assisted drafts come out consistent regardless of who runs them.

- [Sample Changelog Skill file]({{ site.baseurl }}/samples/skill-changelog-viewer/) — purpose, team impact, and the full skill rendered in the browser

## 🛠️ Troubleshooting Portal

Contributed to ideating the troubleshooting portal for Contentstack — templated its structure and pulled supporting information from the customer Salesforce database.

- [Troubleshooting sample page](https://www.contentstack.com/docs/headless-cms/installation-initialization-environments)

## 🎨 Rich Media

Complemented doc articles with explainer diagrams and architecture flows to make complex concepts easier to follow at a glance.

- [Explainer diagram — About Publish Rules](https://www.contentstack.com/docs/headless-cms/about-publish-rules)
- [Architecture flow — Simple Website with Contentstack Launch](https://www.contentstack.com/docs/headless-cms/simple-website-with-contentstack-launch)

## 🔄 End-to-End Doc Flow App

Collaborated on building automation for AI-assisted doc creation, including an app built with Google AI Studio to handle multiple stages of the documentation development lifecycle (DDLC) in one place.

**Demo video**

<video controls preload="metadata" style="width: 100%; max-width: 800px; border-radius: 8px; border: 1px solid rgba(255,255,255,0.08);">
  <source src="{{ site.baseurl }}/samples/docs-ninja-agent-demo.mp4" type="video/mp4">
  Your browser doesn't support embedded video. <a href="{{ site.baseurl }}/samples/docs-ninja-agent-demo.mp4">Download the video</a> instead.
</video>

## 📊 Docsite Analytics

Part of the team that runs docsite analytics using **Microsoft Clarity** and **Swiftype**, to understand how readers actually search and navigate the docs.

- [Sample Clarity Report]({{ site.baseurl }}/samples/clarity-report/) — viewable as tables directly in the browser

## 🖌️ Site Redesign

Contributed to the redesign of the [Contentstack documentation website](https://www.contentstack.com/docs).
