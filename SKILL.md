---
name: tech-doc-html
description: Render technical documents (PRD, ADR/RFC, execution plan, system architecture, design doc, postmortem, roadmap, research report, spec) into polished single-file HTML — from a `.md` source, or directly without one. Trigger when the user wants HTML for any of these doc types, including phrasings like "render as html", "把这份 md 做成 html", or tracking AI-assisted work progress visually.
---

# tech-doc-html

Put technical documents into polished single-file HTML for team sharing and leadership reading.

Origin: Thariq Shihipar (Anthropic Claude Code) argued that HTML should replace Markdown for agent output — it carries higher expression bandwidth. Navigation, folding, filtering, scanning, status-at-a-glance — these are things Markdown simply cannot do. This skill exists because of that gap.

## Spirit

1. **Reshape, don't reskin.** HTML's value is reorganizing for scanning — promote summaries to the top, parallelize side-by-side, let readers jump. If the output reads top-to-bottom like the MD did, this skill failed.
2. **Single file, zero build.** CSS inline, JS inline (vanilla), CDN only for Mermaid. Opens in any browser, attaches to email, shares as a link.
3. **Taste has a floor.** Three hard rules are non-negotiable (see `taste.md`). Above that floor, use judgment.
4. **You already know how to write good HTML.** This skill guides taste and prevents AI-aesthetic convergence. It does not prescribe CSS values or class names — read the example for calibration, then build freely.

## Workflow

1. **Classify** — identify doc type, pick theme (editorial / dashboard) and accent. See the doc-type table in `taste.md`.
2. **Lock audience** — from user input, determine the target reader: engineering-only / cross-functional / leadership. This shapes terminology depth for the entire doc from the first draft, not as post-hoc polish.
3. **Reorganize** — don't translate MD 1:1. Promote TL;DR / metrics to the top. Parallel content (goals vs non-goals, before vs after) goes side-by-side in tables, not sequential bullet lists. Status gets semantic color. TODO/TBD markers render as visible warnings, never silently dropped.
4. **Apply readability** — check the readability speed table in `taste.md`. Apply patterns only where their trigger is met. Don't carpet-bomb.
5. **Pre-flight** — scan against anti-slop hard rules before writing. Verify TOC text = H2 text.
6. **Output** — write next to source MD (`foo.md` -> `foo.html`). Tell the user: theme chosen, reorganization decisions made, source issues worth their attention.

## Save-As repair

If the input `.html` contains `<!-- saved from url= -->` or TOC hrefs starting with `file:///` — it's been through Chrome Save-As. Normalize first (strip comment, rewrite hrefs to `#`), then proceed.

## Execution plans are dashboards

Execution plan HTML is not a one-shot deliverable — it's a working dashboard developers reopen. The dashboard theme's status-glance (progress bar + next up + blocked) is designed for this. `.md` stays source of truth; re-render HTML after status changes.

## CJK documents

When source is >40% CJK: set `<html lang="zh|ja|ko">`, promote CJK fonts in stack, `text-spacing-trim: auto`, body line-height >= 1.85.

## When NOT to use

- Blog / essay / marketing / README -> generic renderer
- Multi-page site -> static site generator
- Slide deck -> pptx skill
- Persuasive pitch with hero sections, scroll animation, dark mode -> frontend-design skill. This skill is for *referential and working* docs, not persuasion pieces.

## Files

- **`taste.md`** — taste guide: hard rules, themes, readability, audience calibration. Read every time.
- **`examples/example-prd.html`** — taste anchor. Read once to calibrate "what good looks like", then build your own.
- **`templates/`** — optional starting point, not mandatory.
- **`references/`** — optional deep-reads for specific decisions.
