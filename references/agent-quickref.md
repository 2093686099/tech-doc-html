# Agent quick reference

One-page decision path. Read this first; deep-read other reference files only when you need details for a specific decision below.

## Workflow at a glance

1. Classify doc-type (table below) → pick theme + accent
2. Set `<html lang>` (zh/ja/ko if >40% CJK chars in source — see `core-tokens.md`)
3. Pick interactivity (table below)
4. Pick readability patterns (table below)
5. Start from `templates/<theme>.html`; fill in per `SKILL.md` Step 5
6. Pre-flight scan against `anti-ai-slop.md` hard rules before writing
7. Write file; tell user what theme + reorganization choices + source issues you noticed

## Doc-type → theme / accent

| Doc type | Theme | Default accent | Detailed reference |
|---|---|---|---|
| PRD / requirements | `editorial` | terracotta | `doc-prd.md` |
| Execution / implementation plan | `dashboard` | forest | `doc-execution-plan.md` |
| System architecture / design doc | `editorial` | ink blue | `doc-architecture.md` |
| RFC / ADR | `editorial` | ink blue or bronze | `doc-rfc-adr.md` |
| Postmortem / retro | `editorial` | burgundy | `doc-postmortem.md` |
| Roadmap | `dashboard` | forest or terracotta | `doc-roadmap.md` |
| Research report | `editorial` (wide column) | bronze | use `doc-prd.md` as template |

If user explicitly overrides theme, respect it. If unclear, ask.

## Top-of-document hierarchy (after H1 + subtitle + meta-row)

**Editorial theme** — TL;DR callout (lift from "Problem" / "Motivation" / "Why") → success metrics as `.metric` callouts → body
**Dashboard theme** — status-glance panel (progress bar + Done/In progress/Blocked/Pending stats + Next up + Blocked) → phase strip → body

See `theme-editorial.md` / `theme-dashboard.md` for the markup.

## Readability patterns — trigger lookup

All defined in `readability.md`. Apply only when trigger is met; don't carpet-bomb.

| # | Pattern | Trigger | Skip when |
|---|---|---|---|
| 1 | Framing sentence below H2 | H2 drops straight into list/table, or title is a noun-phrase | First paragraph already frames |
| 2 | Plain-language term hint | First occurrence ∧ cross-functional reader unlikely to know. ≤12 中文 / 25 拉丁字符 | Title/subtitle already carried the concept; industry-standard term (HTTP, JSON, API) |
| 3 | `.code-faint` for secondary code | Paragraph has ≥4 inline `<code>` blocks at same weight | All code is load-bearing |
| 4 | Dual-layer task name (`.task-name` + `.task-hint`) | Task name >60 chars, or ≥3 inline `<code>`, or only-makes-sense-with-issue-context | Name already scan-friendly |
| 5 | Decision body skeleton (Selection / Why / Cost) | Doc has ADR / decisions section | Single short decision |
| 6 | Risk body skeleton (statement / `<hr>` / mitigation) | Risk callout has both halves | Risk is one-liner |
| 7 | Promote one to `.callout-critical` | 3+ comparable items (risks, decisions, stories) with one load-bearing | <3 items, or no clear leader (sharpen thinking first) |
| 8 | Anchor-by-contrast (before/after) | Problem / Solution section introduces a delta | Reader can infer delta from prose |
| 9 | Hand-tuned inline SVG | Source has spatial structure Mermaid auto-layout can't carry (before/after split, state machine with rich labels, decision tree, parallel pipelines, topology, non-uniform timeline) | Mermaid expresses it adequately; max 2 SVG per doc |

**Token-budget priority order** (if you must cut): keep 1, 2, 3, 6, 7 — they're cheap and high-yield. Defer 4, 5, 8, 9 for short docs or when source is already plain.

Run the **Three-Reader Test** on the proposed output: leader 30s scan / future-you 3 months / cross-functional first-encounter. All three "yes" → render straight; any "no" → apply the corresponding pattern.

## Interactivity decisions

All defined in `interactivity.md`. Always-on: scroll-spy TOC + anchor IDs on H2/H3 + print expansion of `<details>`.

| Source feature | Add | Skip |
|---|---|---|
| Task/risk table >10 rows with status column | Status filter | <10 rows, no categorization |
| 4+ phases × 5+ tasks each | Collapse-by-phase, current open | Short plan |
| 5+ independent items (FAQ, ADRs, risks) | `<details>` per item | <5 items |
| 3+ parallel views (logical/deploy/data) | Tabs | 2 views (side-by-side instead) |
| Anything else | Default static | — |

**Banned**: search box (use Ctrl+F), sort buttons, hover tooltips, real-time fetch, sticky non-TOC, dark-mode toggle, **persistent state (`localStorage` / cookies)** — reader-side ephemeral filter/collapse OK, anything that survives reload is not.

## Anti-slop hard rules — non-negotiable

Defined in `anti-ai-slop.md`. Pre-flight scan before write.

1. **No purple/violet/indigo gradients** anywhere. No `linear-gradient` with those hues; banned hex includes `#8B5CF6`, `#A855F7`, `#6366F1`, `#7C3AED`, anything `#5x-7x` in `R<B` zone.
2. **No emoji** as section icons, status indicators, or decoration. Allowed geometric glyphs: ● ▸ ■ ○ ▲ ✓ ✗ (note: ✓ U+2713, NOT ✅ U+2705).
3. **No `Inter`** (or `Roboto` / `Open Sans` / `Poppins` / `system-ui`) as primary body font. Fallback only.

Soft rules + Images (referential allowed, decorative banned) + good-taste anchors + pre-flight scan list → `anti-ai-slop.md`.

**Images quick rule**: screenshots / hardware photos / team-drawn reference diagrams allowed (wrap in `<figure>` + `<figcaption>` + `alt`, prefer data URL < 200 KB each). AI-generated / stock / decorative / hero banned. Source MD must reference the image — skill never invents.

## Token / class cheat (from `core-tokens.md`)

- `.framing` — italic muted sentence below H2 (Pattern 1)
- `.term-hint` — inline parenthetical (Pattern 2) — **inline only**, not for sublabels
- `.metric-hint` — structural sublabel on metric/status panel — **not** `.term-hint`
- `.task-hint` — task-table sub-line (Pattern 4)
- `code.code-faint` — demoted inline code (Pattern 3)
- `.callout-divider` — hairline inside risk/decision callout (Pattern 6)
- `.callout-critical` — promote one item (Pattern 7)
- `.decision-item` + `.decision-critical` — ADR three-part skeleton (Pattern 5)
- `.status-{done,active,blocked,pending,at-risk,standing}` — text status with geometric prefix. **`standing`** (◇) for items tracked but out-of-scope; **never** roll into `Blocked` counts in status-glance (scope-honest rule, see `anti-ai-slop.md`).
- `.metric-value-text` — modifier for non-numeric metric values (1.5rem vs 2rem)

## When in doubt

- Lean static over interactive
- Lean rendered-as-source over reshaped (don't paraphrase author's words)
- Lean fewer-readability-patterns over carpet-bombing
- Lean editorial-narrow over dashboard-dense when doc-type ambiguous

## What this file is NOT

Not a substitute for the deep-references. When you hit a specific decision, jump to the matching file. This is the index — those are the truth.
