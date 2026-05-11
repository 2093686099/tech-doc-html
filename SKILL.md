---
name: tech-doc-html
description: Render Markdown technical documents (PRD, execution/implementation plan, system architecture, RFC/ADR, design doc, postmortem, roadmap, research report, spec) into polished, human-readable single-file HTML. Use this skill whenever the user has a `.md` file (or several) of any of these types and wants an HTML version for sharing with team or leadership, reviewing, or tracking ongoing work — even if they just say "make this look nice", "render as html", "把这份 md 做成 html", "convert this PRD to a webpage", or similar. ALSO trigger when the user wants to track AI-assisted work progress visually (rendering an execution plan that they update as work proceeds) — this is a primary use case, not a side feature. The skill picks one of two themes (`editorial` for narrative-heavy docs, `dashboard` for status/tracking docs) based on the document type. Output is always a single self-contained `.html` file. Enforces an anti-AI-slop floor (no purple gradients, no emoji-as-icons, no Inter as body font) but allows distinct theme aesthetics within that. The skill judges when to add light interactivity (filtering, collapsing, view switching) based on document size and type.
---

# tech-doc-html

Render Markdown technical documents into polished single-file HTML, with two themes (`editorial` and `dashboard`) and context-aware interactivity. Source of truth stays in MD; HTML is the presentation artifact, regenerated on demand.

## Core principles

1. **Reshape, don't reskin.** HTML's value is in re-organizing for scanning and navigation, not in prettier fonts. If the HTML output reads sequentially top-to-bottom just like the MD did, this skill failed. Promote key info to the top, group parallel content side-by-side, separate "summary" from "detail", let readers jump.

2. **MD is source of truth; the HTML carries no persistent state.** Never modify the source `.md`. The HTML is regenerated from MD. When status changes, the user updates MD and re-renders. Reader-side ephemeral state (filter selections, expanded collapsibles, current tab) is fine because it lives until reload; **persistent state via `localStorage` / cookies is banned** — it competes with MD for authority and grows into "who marked what" semantics that don't belong in a static snapshot. Details in `references/interactivity.md`. This is especially important for execution plans (see "Tracking artifact" below).

3. **Single self-contained file.** All CSS inline. JS inline (vanilla, no frameworks). External CDN only for Mermaid (and Chart.js if data viz is needed, which is rare — see `references/data-viz.md`). Output opens directly in any browser, attaches to email, shares as a link.

4. **Tracking artifact, not one-shot deliverable.** Some documents (especially execution plans, roadmaps) are read many times during work. They are dashboards for the user's own benefit, not just deliverables for stakeholders. The dashboard theme + execution-plan rendering is designed for this — the HTML answers "where are we now, what's next, what's blocked" at a glance.

5. **Two themes, hard anti-slop floor, then flexibility.** Three rules are absolute (no purple gradients, no emoji as section icons, no Inter as primary body font). Beyond that, the two themes diverge: `editorial` is serif + restrained + narrow column; `dashboard` is sans + denser + status-colored. Don't conflate "no AI slop" with "always editorial".

6. **Mobile works but desktop is primary.** Target stakeholder is reading on a laptop. Mobile must not break (responsive layout, collapsible nav, fluid type), but optimize the desktop layout first.

7. **Lift agent-shorthand into human-readable.** The source MD is often written by/for engineers or AI agents — dense with variable names, abbreviations, internal jargon, and issue titles copy-pasted as task names. To a cross-functional reader (leader skimming, PM scanning, future-you in 3 months, new teammate seeing terms for the first time), **agent-shorthand is source code, not rendered output**. It needs reshape before it appears in HTML. Re-rendering is not just layout — it's a *readability layer*: framing sentences below H2s, plain-language hints on first-encounter terms (once per document, only when the cross-functional reader is unlikely to know), visual demotion of secondary code (env names, type names) so load-bearing code stands out, dual-layer task names that reshape verbose issue titles. See `references/readability.md` for the full pattern set. This is what separates "the MD with prettier fonts" from "an HTML someone actually wants to read."

## Workflow

### Step 1 — Classify and pick theme

Look at the source MD's headings, structure, and content. Classify as one of:

| Doc type | Theme | Reference file |
|---|---|---|
| PRD / requirements | `editorial` | `references/doc-prd.md` |
| Execution / implementation plan | `dashboard` | `references/doc-execution-plan.md` |
| System architecture / design doc | `editorial` (mostly) | `references/doc-architecture.md` |
| RFC / ADR | `editorial` | `references/doc-rfc-adr.md` |
| Postmortem / retro | `editorial` | `references/doc-postmortem.md` |
| Roadmap | `dashboard` | `references/doc-roadmap.md` |
| Research report | `editorial` | use `doc-prd.md` as nearest template, but wider column |

**Theme override:** If the user explicitly says "use dashboard theme for this PRD" or similar, respect that. Otherwise pick by type. If unclear, ask.

### Step 2 — Load required references

**Always read first**: `references/agent-quickref.md` — one-page decision path covering theme, accent, interactivity, readability patterns, hard rules, and the token cheat. This is the index; deep-read the references below only when you need details for a specific decision.

For any generation, **always** load (after the quickref):
- `references/core-tokens.md` — shared CSS variables, fonts, spacing
- `references/anti-ai-slop.md` — hard rules / soft rules / good-taste anchors / pre-flight scan (four layers, in priority order)
- `references/readability.md` — patterns for lifting agent-shorthand source into human-readable HTML (framing, plain-language hints, signal layers, dual-layer task names, decision body骨架)
- `references/theme-<editorial|dashboard>.md` — theme-specific tokens and overrides
- The matching `references/doc-<type>.md` file

Conditionally load:
- `references/interactivity.md` — if the document is long enough or has features (filterable lists, multiple views, long FAQs) that benefit from interactivity
- `references/data-viz.md` — only if the source MD contains chart data or the user explicitly asks for charts

### Step 3 — Decide on interactivity (skill's judgment call)

Use the heuristics in `references/interactivity.md`. Quick version:

- **Always add**: TOC with anchor scroll-spy (desktop sticky, mobile collapsible). Cheap and always useful.
- **Add when**: a task/risk/decision table has >10 rows → add status/category filter. Multiple "views" of same info (logical/deployment/data architecture) → tabs. Long FAQ/Q&A → each item collapsible. Execution plan with >15 tasks → status filter + collapse-by-phase.
- **Don't add**: pure narrative prose (PRD problem statement, RFC rationale) doesn't need interactivity. Short docs (<5 sections) work better static.

When in doubt, lean static. Interactivity has a cost: it can hide content from print, mask important details, and break Ctrl+F.

### Step 4 — Re-organize content for the HTML medium

Don't translate MD elements 1:1. Concretely:

- **Top of document** is for promoting summary content, not for headings. Pull the doc's TL;DR / problem statement / key metrics / current status up to the top as visual elements (callouts, metric blocks, progress bars), even if they appear deeper in the MD.
- **Parallel content** (Goals vs Non-goals, In-scope vs Out-of-scope, V1 vs V2, before vs after) → side-by-side table or card pair, NEVER two sequential bullet lists.
- **Comparison tables** (options A/B/C with pros/cons) → real `<table>` not bullet lists.
- **Diagrams** in source → render Mermaid inline. If the source has Mermaid blocks, render them with the CDN loader. If the source describes a flow in prose, do NOT invent a diagram — keep prose. Only render diagrams the source author chose to express as diagrams.
- **Status info** (done/in-progress/blocked/pending tasks) → semantic color + icon, scannable column in tables. For execution plans, also surfaced in the top status-glance.
- **Metrics and dates** → callout blocks with large tabular numerals.
- **TODO/TBD/TK markers** → render as visible warning callouts. Never silently drop them; if they're in the source they're decisions still pending.
- **Reader layering** → for any source that smells like agent-shorthand (dense inline code, unexplained abbreviations, task names copy-pasted from issue titles, no introductory sentences below H2), apply the readability patterns from `references/readability.md`: framing sentences, plain-language hints on first-encounter terms, signal/noise via `.code-faint`, dual-layer task names. Don't rewrite the source's argument — augment the rendering so a cross-functional reader can read it without prior context.

### Step 5 — Generate the HTML

Start from `templates/<theme>.html` (editorial or dashboard) as the skeleton — it carries the head, the core CSS slot, the layout shell, and the print/mobile media queries. Fill in/customize the steps below; don't re-author scaffolding the template already provides.

Build in this order:

1. `<!DOCTYPE html>` + `<head>` with charset, viewport, title from H1.
2. `<style>` block: load core tokens, then theme overrides on top.
3. `<style>` block: `@media (max-width: 768px)` for mobile, `@media print` for print. These are required.
4. Document header: title, optional subtitle, metadata row (owner, status, version, last-updated).
5. **Theme-specific top section**: editorial gets a TL;DR callout; dashboard gets a status-glance (progress bar + counts + Next up + Blocked).
6. Two-column layout: sticky left TOC + main content (desktop); collapsible `<details>` TOC + single-column main (mobile).
7. Body content, reorganized per Step 4.
8. Mermaid CDN loader only if Mermaid blocks present.
9. Vanilla JS for interactivity (filter, collapse, tab switch) only if added per Step 3. Keep small and dependency-free.

### Step 6 — Pre-flight against anti-slop checklist

Before writing the file, scan the proposed output against `references/anti-ai-slop.md`. The three absolute rules:

1. No purple/violet/indigo gradients anywhere
2. No emoji used as section icons, decorative bullets, or status indicators
3. No `Inter` (or `Roboto`, `Open Sans`, `Poppins`, `system-ui`) as the primary body font

Theme-specific anti-slop rules are in each theme's reference file.

### Step 7 — Write and present

Write to `/mnt/user-data/outputs/<doc-name>.html` (or working directory). Name from source filename. Present the file. Tell the user:

- Which theme you picked and why
- What re-organization choices you made (e.g. "I promoted the metrics section to the top, tabbed the architecture views")
- What you noticed in the source that might warrant author attention (TBD markers, undated tasks, missing owner fields)

## Tracking artifact workflow (execution plans)

This is a primary use case worth calling out. The execution plan HTML is not a deliverable — it's a dashboard the developer keeps open while work happens, often AI-assisted work.

Workflow:
1. Developer writes `plan.md` with phases, tasks, statuses, owners, dependencies
2. Developer renders `plan.html` (dashboard theme) — sees status glance, next up, blockers
3. Developer hands a specific task to Claude Code: "do task 3.2 from plan.md"
4. Claude Code does the work, then updates `plan.md` status (`status: done`, `status: in-progress`, etc.) and optionally a `notes:` field with what was done
5. Developer re-renders `plan.html`
6. Status glance now reflects new progress. Next-up list updates. Developer picks the next task.
7. Developer shares `plan.html` link with team/leader for async status updates

For this workflow to work, the dashboard render must:
- Make status changes between renders extremely visible (don't bury them)
- Surface "what's next" prominently — the developer should not have to read the whole doc to know what to delegate next
- Highlight blocked tasks with what's blocking them
- Stay fast to regenerate (which it is — single-file, no build)

See `references/doc-execution-plan.md` for the full pattern.

## Multiple files

If the user provides PRD + execution plan + architecture for one project, render each separately. Optionally produce an `index.html` linking all three — but ask first.

## When NOT to use this skill

- Source is a blog post, essay, marketing copy, README, or general-purpose article — use a generic MD-to-HTML renderer.
- User wants a multi-page website with hosting — that's a static site generator job.
- User wants a slide deck → use the `pptx` skill.
- User wants Word output → use the `docx` skill.
- Source MD describes user-facing UI mockups → use `frontend-design` skill.
- **User wants a persuasive leader pitch / external announcement / marketing-flavored design rationale** (large hero, scroll-revealed sections, hand-crafted diagrams as art, dark mode allowed) → use `frontend-design` skill. tech-doc-html is for *referential and working* docs that get opened, scanned, and re-opened — it deliberately bans hero sections, scroll-reveal animation, and dark mode. Don't fight that boundary; route the request.

## Reference files

Read first (one-page index):
- `references/agent-quickref.md` — decision path + lookup tables + token cheat

Always loaded (deep references):
- `references/core-tokens.md` — shared CSS variables, fonts, spacing
- `references/anti-ai-slop.md` — hard rules / soft rules / good-taste anchors / pre-flight scan
- `references/readability.md` — agent-shorthand → human-readable patterns (single source for Patterns 1-8)
- `references/theme-editorial.md` or `references/theme-dashboard.md` — theme overrides

Per-document-type:
- `references/doc-prd.md`
- `references/doc-execution-plan.md` ← read this carefully; tracking workflow lives here
- `references/doc-architecture.md`
- `references/doc-rfc-adr.md`
- `references/doc-postmortem.md`
- `references/doc-roadmap.md`

Conditional:
- `references/interactivity.md` — when to add what, vanilla JS patterns
- `references/data-viz.md` — Chart.js via CDN; rarely needed

Templates:
- `templates/editorial.html` — base skeleton for editorial theme
- `templates/dashboard.html` — base skeleton for dashboard theme

Examples:
- `examples/example-prd.md` + `.html` — editorial theme output
- `examples/example-exec-plan.md` + `.html` — dashboard theme, tracking artifact
