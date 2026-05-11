# Readability layer

## The core idea

The source MD is usually written **by engineers, for engineers — or by AI agents, for AI agents**. It's dense with variable names, abbreviations, library identifiers, and task titles copy-pasted from issue trackers. That's appropriate for source-of-truth: it's compact, unambiguous, easy to grep.

The HTML, though, is for **humans across functions**: a PM scanning before standup, a director skimming before a review, future-you reopening the doc in three months, a new teammate seeing a term for the first time. They don't have the surrounding context, and they shouldn't have to ask.

This skill's job is to **augment the rendering so cross-functional readers can read it**. The patterns below are how — small, reusable additions that don't change the source's argument, just lower the cost of reading it.

This is what separates "the MD with prettier fonts" from "an HTML someone actually wants to read." It's a rendering decision, not a source rewrite.

## The Three-Reader Test

Before deciding how much readability augmentation to apply, ask three questions of the source:

1. **Leader, 30-second scan.** Will they get the bottom line — what's happening, why now, what's at stake — from the TL;DR, the H2 titles, and one sentence per section?
2. **Future-you, 3 months later.** Will the context of the decisions still be reconstructable, or will the doc require remembering what the room knew at the time?
3. **Cross-functional reviewer, first encounter.** Are the load-bearing terms (acronyms, library names, internal nouns) explained the first time they appear, or does the reader have to look them up?

If any answer is "no", the source benefits from one or more of the patterns below. If all three are "yes", the source is already human-readable — render it straight, don't over-augment.

## Pattern 1 — Framing sentence below each H2

A framing sentence is **one short, plain-language sentence** that sits between an H2 and the body content. It answers: *what does this section do for the reader?*

```html
<h2 id="..."><span class="accent-bar"></span>Section title</h2>
<p class="framing">One sentence stating the section's job in plain language. No code, no acronyms.</p>
<!-- body content (lists, tables, prose) -->
```

```css
.framing {
  font-family: var(--font-serif);   /* or sans, depending on theme */
  color: var(--ink-muted);
  font-size: var(--fs-body);
  margin: 0 0 var(--space-4);
  padding-left: var(--space-3);
  border-left: 2px solid var(--accent-soft);
  font-style: italic;
}
html[lang^="zh"] .framing,
html[lang^="ja"] .framing,
html[lang^="ko"] .framing { font-style: normal; }  /* CJK has no true italics */
```

Use a framing sentence when:
- The section starts with a list or table (no introductory prose)
- The section title is a noun phrase, not a sentence ("Risks", "Decisions", "Phase 2")
- The reader could plausibly ask "why am I reading this section" — answer it preemptively

Don't use when:
- The section's first paragraph already does the framing
- The section is one or two sentences total

## Pattern 2 — Plain-language hint for first-encounter terms

When a term appears for the first time in the document — an acronym, a library name, an internal noun, a vendor product — attach a one-clause plain-language hint inline. Use it once per document; the reader carries the meaning forward.

```html
<p>The system uses <strong>OAuth</strong>
   <span class="term-hint">(industry standard for delegated authorization — third-party login)</span>
   for SSO.</p>
```

```css
.term-hint {
  color: var(--ink-faint);
  font-size: 0.92em;
  font-style: normal;
}
```

**Trigger rule** — hint only when *both* conditions hold:

1. **First occurrence** in the document. Counting includes title, subtitle, TL;DR, body — everything the reader has seen so far. A term hinted on its 4th mention is wasted; the reader has already moved past it without help.
2. **Cross-functional reader unlikely to know.** A vendor product, an internal noun, a library name, a domain acronym that a leader / PM / new teammate would have to look up. Industry-standard terms that the audience definitely knows (HTTP, JSON, API) don't qualify.

Title or subtitle establishes the **name** but rarely the **concept**. If the title is `Foo → Bar 迁移` and `Bar` is a tool a cross-functional reader doesn't recognize, hint `Bar` at its **first body occurrence** — don't repeat on the 3rd or 4th mention. Once per document, once only.

**Length cap**: ≤ 12 Chinese characters / ~25 Latin characters. If the meaning can't compress to that, it's documentation, not a hint. Either drop it and trust context, or promote the term to a glossary footer entry (see optional pattern below).

**Don't**:
- Hint a term whose definition the title or subtitle has already carried. Hint a tool's *role* once at first body mention if needed, but not the bare *name* the title already established.
- Hint a term twice anywhere in the document.
- Use `.term-hint` as a generic faint sublabel inside metric blocks, table cells, status panels, or other structural positions. `.term-hint` is **inline parenthetical** only. For a structural "plain-language version on a sub-line beneath a technical label", use `.metric-hint` (in metric/status blocks) or `.task-hint` (in task tables) — see `core-tokens.md`. The visual is similar; the role isn't, and the class names track the role.

The hint is **inline parenthetical**, not a footnote, not a glossary lookup. Don't break the reader's flow. If you need 20+ terms hinted, use a folded glossary footer (see the optional pattern below) and link.

## Pattern 3 — Signal vs noise via faint code

Inline `<code>` defaults to all-or-nothing visual weight: a config flag name, a library identifier, and a load-bearing API endpoint all look the same. They are not the same — promote a two-tier system:

```css
code              { /* default: load-bearing — SQL DDL, the key API path, the critical type */ }
code.code-faint   { background: transparent; color: var(--ink-muted);
                    padding: 0 0.15em; font-size: 0.88em; }
```

Rules of thumb for which tier to use:

| Use `<code>` (default, prominent) | Use `<code class="code-faint">` (demoted) |
|---|---|
| The exact API path or HTTP verb the doc is about | An env variable name |
| The SQL statement that breaks if wrong | A file path mentioned as an example |
| The exact value the user must set | A type name used as identifier only |
| The key library function or class doing the work | A helper function name mentioned in passing |

When a paragraph contains many inline code blocks, the faint tier reduces visual noise so the load-bearing ones still pop. **If everything is bold, nothing is.**

## Pattern 4 — Dual-layer task / item names

When a list or table entry's "name" is copy-pasted from an issue title (long, dense, full of jargon), split it into two layers — the **name** for scanning, the **hint** for understanding.

```html
<td>
  <span class="task-name">Verify legacy endpoint reachable on v3 server</span>
  <span class="task-hint">Smoke-test 4 namespaces for 200 OK — single firewall for this phase.</span>
</td>
```

```css
.task-name { display: block; line-height: 1.4; }
.task-hint { display: block; font-size: var(--fs-tiny);
             color: var(--ink-faint); margin-top: 0.2rem;
             line-height: 1.35; font-weight: 400; }
```

Trigger this dual-layer when:
- Item name is > ~60 characters
- Item name contains ≥ 3 inline `<code>` blocks
- Item name only makes sense to someone who has read the source issue / spec

Both layers are **rendering output**, not copy-paste from source. The verbose issue title is the agent-shorthand input; the renderer reshapes it into:

- `.task-name` — the scan-friendly version. Short, plain-language phrasing of what the task is, keeping load-bearing identifiers where they aid recognition. Aim for ≤ 50 characters.
- `.task-hint` — one sentence of engineering context: what's actually being done, the constraint that makes it tricky, or what this task unblocks.

A source title like `Langfuse v3.173.x legacy v2 namespace 4-pack 实跑验证 (PR1 0-day 防火墙)` becomes a name of `验证 Langfuse legacy v2 endpoint 在 v3 server 可达` plus a hint `实跑 4 个 namespace 200 OK — 整个 PR1 的 0-day firewall。任一 404/501 即 halt。` Neither layer is the source title verbatim. Both are reshaped at render time. Don't copy-paste the source title into either layer.

This pattern is heaviest in execution plans and roadmaps; see `doc-execution-plan.md` for the full task-table integration.

## Pattern 5 — Decision body: Selection / Rationale / Cost

Decision documentation — ADRs, RFCs, the "Decisions" section of a PRD — benefits from a three-part body skeleton, regardless of length:

```html
<details class="decision-item">
  <summary>
    <span class="decision-title">Decision title</span>
    <span class="decision-meta">Accepted · 2026-05-08</span>
  </summary>
  <div class="decision-body">
    <p><strong>Selection</strong>: what we chose, in one sentence.</p>
    <p><strong>Why</strong>: the constraint or trade-off that drove the choice.</p>
    <p><strong>Cost / risk</strong>: what this commits us to or rules out.</p>
  </div>
</details>
```

The labels can be Selection/Rationale/Cost, or Choice/Why/Trade-off, or 选择/为什么/代价 — pick a single set per document and stay consistent.

Why this works: a reader who only wants the decision reads the Selection. A reviewer judging whether the decision is sound reads Why. A future engineer asking "why doesn't this work for X" reads Cost. Each reader gets what they need without scanning.

For a doc with many decisions, also promote the most consequential one visually — see Pattern 7.

## Pattern 6 — Risk body: Statement, then Mitigation

A risk callout's body has two distinct halves that benefit from explicit separation:

```html
<aside class="callout callout-warn">
  <div class="callout-label">Risk · Short title</div>
  <p>What goes wrong, what makes it likely, what the blast radius is.</p>
  <hr class="callout-divider">
  <p><strong>Mitigation</strong>: how we keep it from happening, or how we recover if it does.</p>
</aside>
```

```css
.callout-divider {
  margin: var(--space-3) 0;
  border: none;
  border-top: 1px solid var(--rule);
}
```

The hairline divider does the work of visually telling "problem statement" apart from "mitigation plan" without needing a second callout or a second label. A reader can skim just the upper halves to enumerate risks, then drop into the lower halves to see how each is being handled.

## Pattern 7 — Promote one item to critical

In any list of comparable items (decisions, risks, tasks), one is usually more consequential than the others. Don't rely on order alone — promote it visually:

```html
<aside class="callout callout-critical">
  <div class="callout-label">Risk #1 · Title</div>
  <!-- body -->
</aside>
```

```css
.callout-critical {
  border-left-color: var(--status-blocked);
  background: color-mix(in srgb, var(--status-blocked) 8%, var(--bg-elevated));
}
.callout-critical .callout-label {
  color: var(--status-blocked);
  font-weight: 700;
}
```

Same idea applies to ADRs (the load-bearing one gets a "必读 / Must-read" flag and brighter framing), to tasks (the firewall task gets row emphasis), to user stories (the headline story gets a wider card).

**Rule of restraint**: at most one item promoted per list. If two compete for "most critical", you haven't sharpened your thinking enough — pick one or re-frame.

## Pattern 8 — Anchor-by-contrast (before / after)

When introducing a new mechanism or approach, ground the reader by **explicitly contrasting** with what came before. Don't make them infer the delta:

- **Prose form**: "Previously, X did Y by means of Z. Now, X does Y by means of W." — two parallel sentences.
- **Table form**: a two-column `table.two-col` with Before | After headers.
- **Diagram form**: a single inline SVG split down the middle, "before" left, "after" right — see Pattern 9 for the SVG rules.

This pattern matters most in the Problem and Solution sections. A diagram of "the painful current shape" next to "the proposed new shape" replaces several paragraphs of explanation.

## Pattern 9 — Hand-tuned inline SVG as first-class illustration

SVG is not just an escape hatch for when Mermaid breaks. It's a distinct medium worth choosing when the source content has **spatial structure that Mermaid's auto-layout can't carry**. Mermaid is the right tool for ~80% of architecture and workflow diagrams (graph, flowchart, sequenceDiagram, gantt, stateDiagram). When the remaining 20% appears, reach for SVG deliberately — not as a fallback, but as the first choice for that content shape.

**Trigger SVG over Mermaid when the source content is one of:**

- **Before / after contrast** (Pattern 8 diagram form) — two halves of the same canvas with a vertical divider, parallel structure visible at a glance. Mermaid can't put two graphs side-by-side with shared aesthetic.
- **State machines with rich transition labels** — Mermaid's `stateDiagram-v2` collapses transition labels and routes edges automatically; if the source's mental model depends on specific transition placement (loops, cross-arrows with annotation), SVG carries it.
- **Decision trees with annotated branches** — the y/n labels on each branch and the leaf-action layout matter. Mermaid renders these as crowded labels.
- **Parallel pipelines that share intermediate storage or rejoin** — auto-layout will cross edges or stretch nodes; the precise spatial relationship between parallel tracks is the point.
- **Topology / network diagrams** — rings, meshes, hub-and-spoke with weighted edges. Geometric placement is information.
- **Timeline strips with non-uniform spacing** — when phases have different lengths, or events cluster at specific points, the spatial scale itself communicates.
- **Source author wrote ASCII art / box-drawing in the MD** — that's a strong signal Mermaid wasn't enough; render the ASCII intent in SVG.

**Don't use SVG when:**

- Mermaid can express it adequately (most graph / flowchart / sequence / gantt cases). Auto-layout that "looks fine" is better than hand-tuned that you'll have to maintain.
- The diagram is decorative — illustration for atmosphere, not for information. The document is paper, not a marketing site.
- You're tempted to use SVG because Mermaid output "looks plain". Mermaid's neutral theme is intentional; lifting cosmetics is not a reason to switch mediums.

**Hard rules when authoring SVG** (inherited from architecture diagrams; apply everywhere SVG appears):

- Maximum 2 hand-tuned SVG diagrams per document. More than that is a signal to redraw in a diagramming tool and embed as a static image (see Images section in `anti-ai-slop.md`).
- **No gradients** of any color. Gradients on technical diagrams read as decorative.
- **No CSS animation, no JS-driven motion.** SVG must be static.
- Use only document tokens for stroke/fill: `var(--ink)`, `var(--ink-muted)`, `var(--rule)`, `var(--accent)`, `var(--accent-soft)`, `var(--bg-elevated)`. No raw hex.
- All visible text inside SVG inherits `var(--font-sans)` (set on the root `<svg>` element). Don't import display fonts.
- Provide `<figcaption>` and `aria-label` on the `<svg>` — screen readers can't parse SVG geometry.
- Set `viewBox` and `preserveAspectRatio="xMidYMid meet"` so it scales with the column.
- **No arrow glyphs in `<text>` labels.** Draw a `<path>` with a marker; don't put `→` in text — arrow glyphs render inconsistently and trigger the anti-slop scanner.
- Wrap in `.diagram-figure` (same pattern as Mermaid figures — see `core-tokens.md`).

This pattern is most often invoked from PRD Problem/Solution sections, architecture documents, and ADRs explaining a structural change. See `doc-architecture.md` for SVG application to system overviews specifically.

## Pre-render lint — quick self-check

Before writing the HTML, scan the proposed output against this checklist. Each "no" suggests applying the corresponding pattern:

1. Does every H2 either lead with prose, or have a framing sentence below it? *(Pattern 1)*
2. On first encounter, does every domain acronym, vendor name, and internal noun have a hint? *(Pattern 2)*
3. In paragraphs with ≥ 4 inline code blocks, is at least one demoted to `.code-faint`? *(Pattern 3)*
4. Does any task/list-item name exceed ~60 characters or contain ≥ 3 code blocks without a `.task-hint` sub-line? *(Pattern 4)*
5. Do ADR / decision bodies use a three-part skeleton (selection / why / cost), or are they undifferentiated prose? *(Pattern 5)*
6. Do risk callouts visually separate "what goes wrong" from "mitigation"? *(Pattern 6)*
7. In lists of 3+ comparable items (risks, decisions, stories), is one promoted to critical — or are they all visually equal? *(Pattern 7)*
8. Does the Problem / Solution introduce the new approach by direct contrast with the old? *(Pattern 8)*

The list isn't a hard gate — sometimes "no" is the right answer (a short doc, a single-decision RFC, prose that's already plain). Use the list as a prompt, not a checklist to mechanically tick off.

## Optional — Reading-mode meta

For long documents (>3 print pages), tell the reader up front what they're walking into. A small chrome line below the metadata row:

```html
<p class="reading-meta">
  <span class="meta-k">Reading time</span> ~6 min ·
  <span class="meta-k">For</span> Leaders + engineers ·
  <span class="meta-k">Depth</span> Spec-level
</p>
```

```css
.reading-meta { font-family: var(--font-sans); font-size: var(--fs-small);
                color: var(--ink-muted); margin-top: var(--space-2); }
.reading-meta .meta-k { color: var(--ink-faint); text-transform: uppercase;
                        font-size: var(--fs-tiny); letter-spacing: 0.06em;
                        margin-right: var(--space-1); }
```

Reading time is rough — calculate at ~250 wpm for English, ~300 cpm for CJK. Round to nearest minute. Audience and depth are author-declared in source MD frontmatter; if absent, omit them, don't guess.

This is **optional**; skip for short docs where it would look ridiculous. Don't invent reading time for a one-page note.

## Optional — Folded glossary footer

When a document has > ~10 first-encounter terms, inline hints get heavy. Promote some terms to a folded glossary at the bottom and link the first occurrence:

```html
<p>The system uses <a href="#gloss-rbac" class="gloss-link">RBAC</a> for permissions.</p>

<!-- at bottom of document -->
<details class="glossary">
  <summary>Glossary</summary>
  <dl>
    <dt id="gloss-rbac">RBAC</dt>
    <dd>Role-Based Access Control — permissions granted by role, not by user. Industry standard.</dd>
    <!-- ... -->
  </dl>
</details>
```

The glossary entry replaces the inline `term-hint`, not in addition to it. Pick one per term — inline for short docs, glossary for long.

Don't auto-generate the glossary from the document; the *author* decides which terms warrant entries. Render-time, the skill just formats what's in the source.

## What this layer is NOT

A few guardrails so this doesn't slide into over-engineering:

- **Not a rewrite.** Don't paraphrase the source. Hints, framings, and dual-layer names are *additions* alongside the source's words, never replacements for them. If the source says "by means of <code>X.Y</code>" and you change it to "using X.Y", you've shifted from rendering to authoring.
- **Not invented content.** Never introduce a fact, a number, a name, a deadline that wasn't in the source. A framing sentence is allowed because it's restating what the section is about; a plain-language hint is allowed because it's defining a term. Inventing a new claim is not allowed.
- **Not editorial commentary.** "This decision is interesting because..." is the skill picking sides. Stay neutral. Frame, don't opine.
- **Not maximalism.** Applying all eight patterns to a short, well-written PRD makes it worse, not better. Use the Three-Reader Test to calibrate. If the source already passes all three, render straight.
- **Source language wins.** If the source MD is in Chinese, write framings and hints in Chinese. If mixed, match the surrounding text. Don't translate.
