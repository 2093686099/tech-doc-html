# taste.md

The floor that keeps agent-generated HTML from converging on the same AI aesthetic. Read once per generation.

## Hard rules

1. **No purple/violet/indigo gradients.** Banned hex includes `#8B5CF6`, `#A855F7`, `#6366F1`, `#7C3AED`.
2. **No emoji as icons.** Not for section headings, status, bullets, or decoration. Geometric glyphs only: `● ▸ ■ ○ ▲ ✓ ✗`
3. **No Inter / Roboto / Open Sans / Poppins as primary font.** Deep fallback only.

## Soft rules

No glassmorphism, no large drop shadows, no every-block-is-a-card, no hero sections, no scroll-reveal, no dark-mode toggle, no `localStorage`/cookies. Images: referential only (screenshots, diagrams from source) — AI-generated, stock, and decorative images are banned.

## Good-taste anchors

Not for imitation — for calibration before you start:
- **Editorial feel**: The Economist, Edward Tufte, Stripe docs, MUJI
- **Dashboard feel**: Linear, GitHub Projects, Vercel, Notion

## Two themes

| | Editorial | Dashboard |
|---|---|---|
| Feel | Magazine article — serif, spacious, restrained | Working dashboard — sans, dense, scannable |
| Column | Narrow (~720px), generous whitespace | Wider (~880px), tighter spacing |
| Signature | TL;DR callout -> metrics -> body | Status-glance (progress + next up + blocked) -> phase strip -> body |
| Suits | PRD, RFC/ADR, architecture, postmortem, research | Execution plan, roadmap |
| Default accent | Terracotta | Forest |

Other accents: ink-blue (architecture, RFC), burgundy (postmortem), bronze (research, ADR).

## Doc-type table

| Doc type | Theme | Accent |
|---|---|---|
| PRD / requirements | editorial | terracotta |
| Execution plan | dashboard | forest |
| Architecture / design doc | editorial | ink blue |
| RFC / ADR | editorial | ink blue or bronze |
| Postmortem | editorial | burgundy |
| Roadmap | dashboard | forest |
| Research report | editorial | bronze |

User override always wins.

## Audience calibration

Identify from user input: **engineering-only** / **cross-functional** / **leadership**.

This determines terminology depth for the entire document. When source is engineering design docs but audience is cross-functional, write in product language from the start — don't layer translation on top of technical prose, just write at the right level.

## Readability patterns

Apply only when the trigger fires. Priority if cutting: keep 1, 2, 3, 6, 7 (cheap, high-yield).

| # | Pattern | Trigger | What to do |
|---|---------|---------|------------|
| 1 | Framing sentence | H2 drops straight into list/table | One muted sentence below H2 explaining the section |
| 2 | Term hint | First use of jargon reader won't know | Inline parenthetical, brief |
| 3 | Code demotion | 4+ inline code at same visual weight | Fade secondary code so key identifiers pop |
| 4 | Task name reshape | Verbose issue title as task name | Short scan name + context hint |
| 5 | Decision skeleton | Decisions / ADR section | Selection / Why / Cost |
| 6 | Risk skeleton | Risk with problem + mitigation | Visually separate the two halves |
| 7 | Promote one | 3+ comparable items, one is critical | One item gets stronger emphasis |
| 8 | Before/after | Section introduces a delta | Side-by-side comparison |
| 9 | Hand-tuned SVG | Spatial structure Mermaid can't carry | Max 2 per doc |

## Interactivity

Always on: scroll-spy TOC (sticky desktop, collapsible mobile), anchor IDs on H2/H3, `<details>` expand on print.

| Add | When | Skip |
|-----|------|------|
| Status filter | Task table >10 rows | <10 rows |
| Collapse-by-phase | 4+ phases x 5+ tasks | Short plan |
| `<details>` per item | 5+ independent items | <5 items |
| Tabs | 3+ parallel views | 2 views — use side-by-side |

When in doubt, static.

## Three-Reader Test

Before output, verify it works for all three:
1. **Leader** — 30s scan, gets the point
2. **Future-you** — 3 months later, no context loss
3. **Cross-functional newcomer** — first encounter, no domain knowledge

Any "no" -> apply the matching readability pattern.

## Pre-flight

- No purple/violet gradients in CSS
- No emoji as icons
- No Inter/Roboto/Open Sans as primary font
- TOC text = H2 text
- Status counts honest about scope (active work vs deferred items not mixed)
