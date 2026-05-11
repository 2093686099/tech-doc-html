# Document type: PRD / requirements doc

**Theme:** `editorial` (default). Override only if the user has many tables/dashboards and the doc is more like a tracker than a spec.

**Accent:** terracotta (default) or ink blue if technically dense.

Audience: PM, design, stakeholders, leadership. Skimming on a laptop or projecting on a wall during review. They want to absorb intent fast.

## Reader layering

PRDs are written cross-functionally — engineers write them but PMs, designers, and leaders read them. They sit at the boundary where agent-shorthand source MDs hurt the most: a Solution section full of class names and config flags is dense for the author but opaque to half the audience.

Apply `references/readability.md` patterns aggressively on PRDs:

- **Framing sentence below every H2** (readability Pattern 1) — especially below Problem, Solution, Risks, Open questions
- **Plain-language hint on first encounter** (Pattern 2) — every vendor product, internal noun, domain acronym gets a one-clause hint the first time, when the audience is unlikely to know it. See Pattern 2's tightened trigger: first occurrence AND cross-functional reader unlikely to know
- **Cross-cutting doc-internal shorthand inside the TL;DR** — PRDs frequently invent project-internal compounds (`G2/G3/G4 三闸`, `Triple-Gate`, `Phase -1 firewall`, internal status names) and use them in the TL;DR before defining them in body sections. The TL;DR is read first and often read alone; treat its first mention of any such compound as the relevant "first occurrence" for Pattern 2 and inline-gloss it once. Don't push the reader 3 sections down to find what the acronym means
- **Signal/noise via `.code-faint`** (Pattern 3) — promote the load-bearing call (the actual contract, the headline endpoint) and demote secondary code (env names, type names, file paths)
- **Anchor-by-contrast** (Pattern 8) — Problem and Solution sections benefit enormously from a "before / after" table or single inline SVG

Run the Three-Reader Test before considering the PRD done.

## Information hierarchy

Top of document, in order — promote whatever exists in source:

1. **Title** (H1, sans, weight 700)
2. **One-line summary subtitle** (italic serif, ~1.25rem). Pull from first paragraph if not explicit.
3. **Metadata row**: owner, status (draft/review/approved), version, last-updated, target launch
4. **TL;DR callout**: if source has "Problem" / "Motivation" / "Why this matters", lift the key paragraph here
5. **Success metrics** as `.metric` callout blocks. KPIs are the headline; promote them.

## Structural patterns

### Problem statement

If present, render as a `.callout` block (label: "PROBLEM" or "MOTIVATION") near the top. Don't bury in body prose.

### Goals vs Non-goals → side-by-side `table.two-col`

Never render as two sequential bullet lists; the contrast IS the value.

```html
<table class="two-col">
  <thead><tr><th>Goals</th><th>Non-goals</th></tr></thead>
  <tbody><tr><td>...</td><td>...</td></tr></tbody>
</table>
```

Same pattern for: In-scope vs Out-of-scope, V1 vs V2, This launch vs Future.

### User stories → `.story-grid` cards

If ≥3 user stories: render as 2-column grid of cards. Each card has:
- Persona label (small caps, sans, muted)
- "As a... I want... so that..." in italic serif
- Acceptance criteria as a short list below, optionally separated by a hairline

If only 1–2 stories: render as full-width prose sections, not cards.

### Success metrics → `.metric` callouts

Pull every concrete number (% target, count, latency threshold, date) into `.metric` blocks at the top of the relevant section. Use `.metrics-row` for layout.

### Personas

Compact rows: name + role on left, short description on right. **No AI-generated avatar images**. No emoji. If you must mark a persona visually, use the initial in a small text circle or skip.

### Open questions / risks → warning callouts

Each gets its own `.callout.callout-warn` with the accent border. These should visually pop because they're action items for the reviewer.

**Risk body skeleton** — each risk uses the statement / mitigation skeleton separated by a hairline `<hr class="callout-divider">`. A reviewer can scan just the statements to enumerate risks, then drop into the mitigations. See `readability.md` Pattern 6 for the full markup.

When there are 3+ risks, promote the most consequential one to `.callout-critical` — at most one per list. See `readability.md` Pattern 7.

### Decisions / ADR-style entries inside a PRD

Some PRDs have a "Key architecture decisions" section summarizing N ADRs. Render them using the three-part body skeleton — Selection / Rationale / Cost — see `readability.md` Pattern 5 for the full skeleton and the `<details class="decision-item">` markup.

When there are 3+ decisions and one is load-bearing (e.g. its prerequisite blocks the whole project), promote it to `.decision-critical` — open by default, brighter framing, optional "Must-read" tag in the summary. See `readability.md` Pattern 7.

### User flow → numbered sequence

If the source describes a flow as a numbered list:

```html
<ol class="flow">
  <li><span class="flow-num">01</span><div class="flow-body">User opens the app</div></li>
  <li><span class="flow-num">02</span><div class="flow-body">...</div></li>
</ol>
```

```css
.flow { list-style: none; padding: 0; counter-reset: none; }
.flow li { display: flex; gap: var(--space-6); margin-bottom: var(--space-6);
           align-items: baseline; }
.flow-num { font-family: var(--font-sans); font-size: 1.5rem; font-weight: 600;
            color: var(--accent); font-variant-numeric: tabular-nums;
            min-width: 2.5rem; }
.flow-body { flex: 1; padding-top: var(--space-1); }
```

## Interactivity decisions for PRDs

- Default: static (PRDs are read, not consulted).
- Add tabs if the PRD explicitly compares 3+ alternatives.
- Add collapsibles if the PRD has a long FAQ or open-questions list (10+ items).
- Skip filters — PRDs don't have filterable content.

## Don'ts

- Don't add a "Next steps" or "Conclusion" if source doesn't have one. The skill organizes; doesn't author.
- Don't reorder the source's logical narrative flow — you can promote summary content to the top, but don't rewrite the doc's argument.
- Don't add stock illustrations, hero images, or AI-generated graphics. Referential images (UI screenshots, hardware photos, team-drawn architecture sketches) are allowed — see `anti-ai-slop.md` Images section for the rules.
- Don't add emoji to personas (no 👤 👩‍💼).
