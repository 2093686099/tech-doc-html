# tech-doc-html

A Claude skill that turns agent output into single-file HTML reports — for plans you'll actually re-open, PRDs your team will actually read, and reviews that survive Monday morning.

## The problem

[Thariq Shihipar](https://x.com/trq212/status/2052811606032269638), an Anthropic engineer on Claude Code, made the case last month: HTML is the new Markdown for agent output. The argument is hard to dismiss.

- Nobody reads a Markdown file longer than a hundred lines. Opus 4.7 routinely produces plans, PRDs, and reviews that run to several hundred.
- Markdown is a static report. It can't filter, sort, fold, jump between sections, or track status — the things you actually do with agent output.
- Browsers don't render Markdown natively. Sharing a `.md` with a non-developer stakeholder is a friction point that compounds across a team.
- HTML — single-file, with tables, SVG, collapsibles, and inline interaction — picks up exactly where Markdown drops off.

But asking Claude to "write me an HTML report" introduces a different problem: LLM-generated HTML drifts to a recognizable AI aesthetic by default, and one template renders a PRD with the same gestalt as a migration plan. The format is right; the substance isn't there yet.

That's what this skill is for.

## Why this skill

### 1. Anti-slop guardrails are explicit lint rules, not vibes

Default LLM-generated HTML drifts toward a viral-demo aesthetic. This skill ships four explicit layers of taste enforcement:

- **Hard rules** — must follow. Specific gradient, font, and iconography bans, with concrete examples of what gets rejected.
- **Soft rules** — strongly recommended. Default patterns to avoid, with the reasoning.
- **Good-taste anchors** — concrete references the output aims at.
- **Pre-flight scan** — a checklist Claude runs against its own output before delivery.

Other "ask Claude for HTML" approaches gamble on taste. This one fails the pre-flight scan when it drifts.

### 2. Doc-type aware, not one template fits all

A PRD shouldn't share its layout with an execution plan. An ADR isn't a roadmap. The skill carries five doc-type-specific reference files, each tuning three things:

- **Hierarchy** — what goes at the top, and in what order
- **Pattern priority** — which of the 9 readability patterns matter most for this doc type
- **Theme default** — editorial (long-form reading) or dashboard (status-dense)

A PRD leads with problem statement and decisions. An execution plan leads with status and timeline. Specialized rather than generic.

### 3. Markdown is the source of truth; HTML is a regenerated snapshot

[Thariq himself acknowledged](https://simonwillison.net/2026/May/8/unreasonable-effectiveness-of-html/) the editability cost of HTML — it's hard to co-author, hard to Git-review line-by-line. This skill sidesteps the cost by treating the relationship as one-way:

1. Write and edit the `.md`
2. Render to HTML
3. Share the HTML for review and tracking
4. Feedback flows back to the `.md`, not the HTML
5. Re-render

Reader-side persistent state (`localStorage`, `sessionStorage`, cookies) is **banned** for the same reason: any persisted state competes with the MD for authority. The HTML is a snapshot, not a database.

## What you get

- **9 readability patterns** with explicit triggers (`use when X, don't use when Y`) — including hand-tuned inline SVG promoted to a first-class illustration medium
- **5 doc types** — PRD, ADR, execution plan, roadmap, postmortem — each with its own reference file
- **2 themes** — editorial (long-form) and dashboard (status-dense)
- **Single-file output** — CSS, SVG, and JS all inline; only Mermaid loads from CDN
- **Image rules** — referential images (screenshots, hardware photos, team sketches) allowed; AI-generated, stock, and decorative banned
- **Out of scope, explicit** — persistent state, multi-file sites, real-time dashboards are not what this skill does, and the docs say so

## For agent-driven developers

If you use Claude Code, Codex, Cursor, or another agent for real work, four concrete uses:

1. **Track agent plans as HTML dashboards.** Have your agent emit its multi-step plan as a dashboard-themed HTML. Filter by status, jump to blocked tasks, re-render after each iteration. The plan becomes a workspace, not a wall of bullets.

2. **PRDs your team will read.** Agent drafts the PRD; you render it to HTML; stakeholders click through decisions and rationale instead of scrolling four hundred lines of MD.

3. **PR reviews without IDEs.** Color-coded by severity, grouped by file, with inline annotations. A non-developer reviewer doesn't need to open VS Code.

4. **Migration plans, retros, postmortems.** Timeline + decision rationale + risk log in one shareable file. Stays readable a year later when someone reopens it.

## Install

### Claude Code

```bash
cp -r tech-doc-html ~/.claude/skills/
```

Claude picks it up automatically when a task matches. You can also invoke explicitly: `/skills tech-doc-html`.

### Claude.ai (Projects)

Create a Project, upload `SKILL.md` and the `references/` directory to project knowledge.

### Other agents (Codex, Cursor, etc.)

Use `references/` as a system-prompt asset. The directory is structured so each file is independently loadable based on which doc type Claude is producing.

## Examples

Two generic examples in `examples/`:

- **`example-prd.md` → `example-prd.html`** — PRD for adding dark mode to a hypothetical open-source editor (editorial theme)
- **`example-plan.md` → `example-plan.html`** — Postgres 13 → 16 migration execution plan (dashboard theme)

Open the `.html` files in a browser. The `.md` files are the inputs Claude was given.

## Where Markdown still wins

This skill isn't a Markdown replacement. `.md` remains the right tool for:

- READMEs (this file is in MD — GitHub renders it natively)
- Slack and Discord snippets (code fences are universal)
- RAG document corpora (LLMs parse MD more reliably than HTML)
- Files with active multi-author Git history (line-by-line blame matters)
- Personal scratch notes

Reach for this skill when output needs **repeated reading, team review, status tracking, comparison, filtering, or follow-up editing** — Thariq's own carve-out rule.

---

Built on the design principles laid out in [Thariq Shihipar's HTML thread](https://x.com/trq212/status/2052811606032269638) and [Simon Willison's analysis](https://simonwillison.net/2026/May/8/unreasonable-effectiveness-of-html/). The skill takes that thesis and answers the "but LLM HTML is ugly / one-template-fits-all / hard to maintain" rebuttals.
