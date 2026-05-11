# Core design tokens

These tokens are shared between both themes. Theme-specific overrides go in `theme-editorial.md` and `theme-dashboard.md`.

## Spacing scale (shared)

```css
:root {
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-6: 1.5rem;
  --space-8: 2rem;
  --space-12: 3rem;
  --space-16: 4rem;
  --space-24: 6rem;
}
```

## Typography stack (shared resources)

```css
:root {
  --font-serif: 'Charter', 'Source Serif Pro', 'Source Serif 4', 'Iowan Old Style',
                'Apple Garamond', 'Baskerville', 'Times New Roman',
                'Source Han Serif SC', 'Noto Serif CJK SC', serif;
  --font-sans:  'Söhne', 'IBM Plex Sans', -apple-system, 'Helvetica Neue',
                'PingFang SC', 'Hiragino Sans GB', sans-serif;
  --font-mono:  'JetBrains Mono', 'Fira Code', 'SF Mono', 'Menlo', monospace;
}
```

Each theme picks which stack is primary for body, which for chrome (headings/labels/metadata).

**Important:** `Inter` is intentionally NOT in this stack. It's banned by the anti-slop rules.

### CJK-primary documents

When the source document is primarily Chinese / Japanese / Korean, the Latin display fonts (Charter, Söhne) miss most glyphs and the browser falls through to the fallback chain, which causes per-glyph font-mixing — looks unprofessional. If the `<html lang>` attribute starts with `zh`, `ja`, or `ko`, promote the CJK system fonts to the front of the stack:

```css
html[lang^="zh"], html[lang^="ja"], html[lang^="ko"] {
  /* CJK font first; Latin shapes fall through to Söhne/Charter via fallback */
  --font-sans:  'IBM Plex Sans', 'PingFang SC', 'Hiragino Sans GB',
                'Microsoft YaHei', 'Source Han Sans SC',
                'Noto Sans CJK SC', sans-serif;
  --font-serif: 'Source Han Serif SC', 'Noto Serif CJK SC',
                'PingFang SC', 'Charter', serif;
  /* CJK ideographs are visually taller — bump line-height to keep breathing room */
  --lh-body:    1.75;
}
```

Set `<html lang="zh-CN">` (or `lang="zh"`, `ja`, etc.) on the output. Render-time decision: scan the source MD; if >40% of characters are in the CJK Unified Ideographs range (U+4E00–U+9FFF), set the lang attribute and the override kicks in automatically.

The Latin fallback still happens for ASCII strings (variable names, English headings interleaved into Chinese prose) — both stacks degrade gracefully.

## Semantic status colors (used by dashboard theme)

```css
:root {
  --status-done:    #5A7A5A;   /* muted green-gray, not bright */
  --status-active:  #B85C38;   /* terracotta — same as accent */
  --status-blocked: #A8463A;   /* deep red, not fire-engine */
  --status-pending: #8A8A8A;   /* faint gray */
  --status-at-risk: #8A6B3A;   /* amber-bronze */
}
```

These are semantic — they mean exactly status. They are not theme colors. Don't use them for anything except task/work status.

## Accent palette (pick one per doc, applies to either theme)

| Name | `--accent` | `--accent-soft` | Suits |
|---|---|---|---|
| Terracotta (default) | `#B85C38` | `#F4E8E0` | PRDs, general |
| Ink blue | `#1F3A5F` | `#E2E8EE` | Architecture, technical specs |
| Forest | `#2C5F3F` | `#E0E9E3` | Execution plans, ops |
| Burgundy | `#7A1F2B` | `#EFE0E2` | Postmortems, retros |
| Bronze | `#8B6B3A` | `#EFE8D9` | Research, strategy |

**Rule:** one accent per document. Don't mix. If unsure, default to terracotta.

The accent appears on: links, the small bar next to H2, metric numbers, status-active text, callout left-borders. Never as a full-block background.

## Shared component styles

These are theme-agnostic and used by both themes (theme-specific tokens fill in `--bg`, `--ink`, etc.):

### Accent bar before H2

```html
<h2 id="..."><span class="accent-bar"></span>Section title</h2>
```
```css
.accent-bar { display: inline-block; width: 3px; height: 0.85em;
              background: var(--accent); margin-right: var(--space-3);
              vertical-align: -0.05em; }
```

### Metric callout

```html
<div class="metric">
  <div class="metric-value">42%</div>
  <div class="metric-label">Activation lift</div>
</div>
```
```css
.metric { display: inline-block; padding: var(--space-4) var(--space-6);
          background: var(--accent-soft); border-left: 3px solid var(--accent); }
.metric-value { font-family: var(--font-sans); font-size: 2rem; font-weight: 600;
                font-variant-numeric: tabular-nums; line-height: 1; color: var(--ink); }
.metric-label { font-family: var(--font-sans); font-size: var(--fs-small);
                color: var(--ink-muted); margin-top: var(--space-2); }
.metrics-row { display: flex; flex-wrap: wrap; gap: var(--space-4); margin: var(--space-6) 0; }
```

**Non-numeric metric values.** When the success criterion is qualitative ("无退化" / "stable" / "passing") rather than a number, use the `.metric-value-text` modifier to drop the size from 2rem to 1.5rem — a short phrase at 2rem reads as a colossal headline.

```html
<div class="metric-value metric-value-text">无退化</div>
```
```css
.metric-value.metric-value-text { font-size: 1.5rem; }
```

Don't fake a number to fit the layout (`≥ 0` to represent "no degradation"). The slot should be honest about what it's measuring; size the visual to fit a phrase.

### Callout / note / warning

```html
<aside class="callout">
  <div class="callout-label">Note</div>
  <p>...</p>
</aside>
<aside class="callout callout-warn">
  <div class="callout-label">Risk</div>
  <p>...</p>
</aside>
```
```css
.callout { padding: var(--space-4) var(--space-6); margin: var(--space-6) 0;
           border-left: 3px solid var(--rule-strong); background: var(--bg-elevated); }
.callout-warn { border-left-color: var(--accent); }
.callout-label { font-family: var(--font-sans); font-size: var(--fs-tiny);
                 text-transform: uppercase; letter-spacing: 0.08em;
                 color: var(--ink-muted); margin-bottom: var(--space-2); }
.callout > p:last-child { margin-bottom: 0; }
```

### Metadata row

```html
<div class="meta-row">
  <span><span class="meta-k">Owner</span>Alice</span>
  <span><span class="meta-k">Status</span>Draft</span>
  <span><span class="meta-k">Updated</span>2026-05-10</span>
</div>
```
```css
.meta-row { display: flex; flex-wrap: wrap; gap: var(--space-6);
            font-family: var(--font-sans); font-size: var(--fs-small);
            color: var(--ink); margin-top: var(--space-4); }
.meta-k { color: var(--ink-faint); text-transform: uppercase;
          font-size: var(--fs-tiny); letter-spacing: 0.06em; margin-right: var(--space-2); }
```

### Status text indicators

```html
<span class="status status-done">Done</span>
<span class="status status-active">In progress</span>
<span class="status status-blocked">Blocked</span>
<span class="status status-pending">Pending</span>
```
```css
.status { font-family: var(--font-sans); font-size: var(--fs-small); font-weight: 500;
          text-transform: uppercase; letter-spacing: 0.04em;
          font-variant-numeric: tabular-nums; white-space: nowrap; }
.status-done    { color: var(--status-done); }
.status-done::before { content: "● "; }
.status-active  { color: var(--status-active); font-weight: 600; }
.status-active::before { content: "▸ "; }
.status-blocked { color: var(--status-blocked); font-weight: 600; }
.status-blocked::before { content: "■ "; }
.status-pending { color: var(--status-pending); }
.status-pending::before { content: "○ "; }
.status-at-risk { color: var(--status-at-risk); font-weight: 600; }
.status-at-risk::before { content: "▲ "; }
.status-standing { color: var(--ink-muted); font-weight: 500; }
.status-standing::before { content: "◇ "; }
```

These status indicators use **geometric shapes**, not emoji. That keeps them through copy-paste, print, and renders consistently across platforms. The colors carry the meaning; the shapes are redundant for accessibility.

**`status-standing`** is for items tracked alongside active work but **categorically outside its scope** — follow-up issues that don't block the current release, items deferred to a later milestone, "we know about it, we'll handle it later" backlog. The diamond glyph (`◇`) distinguishes it visually from active states; the muted color says "informational, not actionable".

Don't reuse `status-blocked` for these. `Blocked` means "something is stopping this from running"; standing items aren't running because they were deliberately deferred. A status-glance panel that shows `Blocked: 2` when the 2 items are out-of-scope follow-ups will mislead a leader's 30-second scan — see the "scope-honest status counts" rule in `anti-ai-slop.md`.

### Date pill (monospace, accent)

```html
<span class="date">May 24</span>
```
```css
.date { font-family: var(--font-mono); font-size: var(--fs-small);
        color: var(--accent); font-variant-numeric: tabular-nums; }
```

## Readability layer tokens

These classes implement the readability patterns described in `references/readability.md`. They're theme-agnostic — they use the shared semantic tokens (`--ink-muted`, `--ink-faint`, `--accent-soft`, `--rule`) and inherit the document's font stack. Both themes consume them as-is.

### `.framing` — sentence below an H2

```html
<h2><span class="accent-bar"></span>Section title</h2>
<p class="framing">One sentence in plain language stating what this section does for the reader.</p>
```
```css
.framing { color: var(--ink-muted); font-size: var(--fs-body);
           margin: 0 0 var(--space-4);
           padding-left: var(--space-3);
           border-left: 2px solid var(--accent-soft);
           font-style: italic; }
html[lang^="zh"] .framing,
html[lang^="ja"] .framing,
html[lang^="ko"] .framing { font-style: normal; }
```

In the editorial theme, `.framing` inherits the serif body. In dashboard, it inherits sans. No theme-specific override needed.

### `.term-hint` — inline plain-language hint for a first-encounter term

```html
<p>The system uses <strong>OAuth</strong>
   <span class="term-hint">(delegated authorization — third-party login)</span>
   for SSO.</p>
```
```css
.term-hint { color: var(--ink-faint); font-size: 0.92em; font-style: normal; }
```

Use once per document per term. See `readability.md` Pattern 2 for the trigger rule (first occurrence ∧ cross-functional reader unlikely to know).

### `.metric-hint` — structural sublabel inside metric / status blocks

Distinct from `.term-hint`. Use when a metric label or panel header has both a technical name and a plain-language restatement on the line below it. The hint is **structural** — it occupies its own line beneath the label, not an inline parenthetical inside prose.

```html
<div class="metric-label"><strong>G2</strong> · Trace 覆盖<br>
  <span class="metric-hint">所有 LLM 调用都能被 Langfuse 看到</span></div>
```
```css
.metric-hint { color: var(--ink-faint); font-size: 0.92em; }
```

Don't reuse `.term-hint` for this position. `.term-hint` is inline parenthetical inside running prose (Pattern 2); `.metric-hint` is a structural sub-line on a label. The visual is similar; the role isn't, and keeping the class names role-aligned prevents abuse drift over time.

### `code.code-faint` — demoted inline code

The two-tier code system: default `<code>` is for load-bearing identifiers; `<code class="code-faint">` is for secondary code (env names, type names, file paths mentioned in passing).

```css
code.code-faint { background: transparent; color: var(--ink-muted);
                  padding: 0 0.15em; font-size: 0.88em; }
```

See `readability.md` Pattern 3 for which tier to use.

### `.task-name` / `.task-hint` — dual-layer task name

For task tables where the engineer-facing name is dense but the reader benefits from a plain-language sub-line:

```html
<td>
  <span class="task-name">Engineer-facing task name (preserve identifiers)</span>
  <span class="task-hint">One-sentence plain-language description for scanners.</span>
</td>
```
```css
.task-name { display: block; line-height: 1.4; }
.task-hint { display: block; font-size: var(--fs-tiny);
             color: var(--ink-faint); margin-top: 0.2rem;
             line-height: 1.35; font-weight: 400; }
```

See `doc-execution-plan.md` for the integration into task tables; `readability.md` Pattern 4 for the trigger criteria.

### `.callout-divider` — hairline inside risk / decision callouts

Separates "what goes wrong" from "mitigation" inside a single callout — no second label needed.

```html
<aside class="callout callout-warn">
  <div class="callout-label">Risk · Title</div>
  <p>Problem statement.</p>
  <hr class="callout-divider">
  <p><strong>Mitigation</strong>: how we handle it.</p>
</aside>
```
```css
.callout-divider { margin: var(--space-3) 0; border: none;
                   border-top: 1px solid var(--rule); }
```

### `.callout-critical` — promote the load-bearing item

For lists of 3+ comparable items (risks, decisions, stories) where one is consequentially more important. **At most one per list.**

```css
.callout-critical {
  border-left-color: var(--status-blocked);
  background: color-mix(in srgb, var(--status-blocked) 8%, var(--bg-elevated));
}
.callout-critical .callout-label {
  color: var(--status-blocked); font-weight: 700;
}
```

When the document doesn't carry a "danger" semantic (a PRD decisions section is informational, not risk), use `--accent` and `--accent-soft` instead of `--status-blocked` to keep the visual promotion without implying danger:

```css
.callout-critical.callout-positive {
  border-left-color: var(--accent);
  background: var(--accent-soft);
}
.callout-critical.callout-positive .callout-label { color: var(--accent); }
```

### `.decision-item` — three-part body skeleton for ADRs / decisions

```html
<details class="decision-item">
  <summary>
    <span class="decision-title">Decision title</span>
    <span class="decision-meta">Accepted · 2026-05-08</span>
  </summary>
  <div class="decision-body">
    <p><strong>Selection</strong>: one sentence.</p>
    <p><strong>Why</strong>: the driving constraint or trade-off.</p>
    <p><strong>Cost / risk</strong>: what this commits us to or rules out.</p>
  </div>
</details>
```
```css
.decision-item { border-bottom: 1px solid var(--rule);
                 padding: var(--space-3) 0; margin: 0; }
.decision-item summary { cursor: pointer; list-style: none;
                         display: flex; justify-content: space-between;
                         align-items: baseline; gap: var(--space-3); }
.decision-item summary::-webkit-details-marker { display: none; }
.decision-item summary::before {
  content: "▸"; color: var(--accent);
  margin-right: var(--space-3);
  display: inline-block; width: 1em; text-align: center;
  transition: transform 0.12s ease;
}
.decision-item[open] summary::before { transform: rotate(90deg); }
.decision-title { font-weight: 500; }
.decision-meta { font-family: var(--font-sans); font-size: var(--fs-tiny);
                 color: var(--ink-faint); text-transform: uppercase;
                 letter-spacing: 0.06em; white-space: nowrap; }
.decision-body { padding: var(--space-3) 0 var(--space-2) calc(var(--space-6) + 0.25em);
                 font-size: var(--fs-small); }
.decision-body p { margin-bottom: var(--space-3); }
```

Use a rotating triangle (`▸`/`▾`) for the open indicator — both glyphs have identical widths so the title doesn't jitter when toggled. Don't use `+`/`−`: U+002B and U+2212 render at different widths.

To promote one decision visually (the "must-read" / load-bearing one), add `.decision-critical`:

```css
.decision-critical {
  background: var(--accent-soft);
  padding: var(--space-3) var(--space-4);
  border: 1px solid var(--accent);
  margin-bottom: var(--space-2);
}
.decision-critical .decision-title { font-weight: 600; }
.decision-flag {
  display: inline-block; font-family: var(--font-sans);
  font-size: var(--fs-tiny); text-transform: uppercase;
  letter-spacing: 0.08em; color: var(--accent);
  padding: 0 var(--space-2); border: 1px solid var(--accent);
  margin-right: var(--space-2); vertical-align: 0.1em; font-weight: 600;
}
```

```html
<details class="decision-item decision-critical" open>
  <summary>
    <span class="decision-title">
      <span class="decision-flag">Must read</span>
      Decision title
    </span>
    <span class="decision-meta">Accepted · 2026-05-08</span>
  </summary>
  <!-- body -->
</details>
```

### `.reading-meta` — optional reading-time meta line

For long documents (>3 print pages), tells the reader what they're walking into:

```html
<p class="reading-meta">
  <span class="meta-k">Reading time</span> ~6 min ·
  <span class="meta-k">For</span> Leaders + engineers
</p>
```
```css
.reading-meta { font-family: var(--font-sans); font-size: var(--fs-small);
                color: var(--ink-muted); margin-top: var(--space-2); }
.reading-meta .meta-k { color: var(--ink-faint); text-transform: uppercase;
                        font-size: var(--fs-tiny); letter-spacing: 0.06em;
                        margin-right: var(--space-1); }
```

Reading time at ~250 wpm English / ~300 cpm CJK. Author-declared in source frontmatter; omit if absent.

### `::selection` — branded text selection

```css
::selection { background: var(--accent-soft); color: var(--ink); }
```

Small detail that matters when readers scan and highlight. The accent-soft background is gentle enough not to fight the document's content but distinct enough that "selected" is unambiguous, even when the OS's default selection color (often a hard blue) would clash with the theme accent.

### Pre-flight `text-spacing-trim` for CJK

In the CJK font block (see "CJK-primary documents" above), add `text-spacing-trim: auto` — a progressive-enhancement hint to recent Safari/Chrome that improves spacing between CJK and Latin characters at glyph boundaries. Browsers that don't support it ignore it; no fallback needed.

```css
html[lang^="zh"], html[lang^="ja"], html[lang^="ko"] {
  /* ...existing CJK font and lh-body overrides... */
  text-spacing-trim: auto;
}
```

### Mermaid diagram figure

```html
<figure class="diagram-figure">
  <div class="mermaid">graph LR ...</div>
  <figcaption>Figure 1. High-level system overview.</figcaption>
</figure>
```
```css
.diagram-figure { margin: var(--space-8) 0; padding: var(--space-6);
                  background: var(--bg-elevated); border: 1px solid var(--rule);
                  max-width: var(--content-max-wide); }
.diagram-figure .mermaid { display: flex; justify-content: center; }
figcaption { font-family: var(--font-sans); font-size: var(--fs-small);
             color: var(--ink-muted); text-align: center;
             margin-top: var(--space-4); }
```

## Mermaid loader (drop-in)

Only include if Mermaid blocks are present in output:

```html
<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: true, theme: 'neutral',
    themeVariables: { fontFamily: 'inherit' } });
</script>
```

Use `theme: 'neutral'`. Never override Mermaid colors to fit the theme — let it stay neutral so it's readable, prints cleanly, and doesn't fight the document's accent.

## Print baseline (shared)

Both themes must include:

```css
@media print {
  body { font-size: 11pt; line-height: 1.5; background: #FFFFFF !important; }
  .page { display: block; padding: 0; }
  nav.toc, .toc-mobile, .filter-bar { display: none; }
  main { max-width: 100%; }
  h2, h3 { page-break-after: avoid; }
  table, figure, .callout, .metric, .diagram-figure, .story, .panel {
    page-break-inside: avoid;
  }
  details { open: ''; }  /* expand all collapsibles for print */
  details summary { display: none; }
  a { color: var(--ink); border-bottom: none; text-decoration: underline; }
  a[href^="http"]::after { content: " (" attr(href) ")"; font-size: 9pt;
                           color: var(--ink-muted); }
  pre { white-space: pre-wrap; word-wrap: break-word; }
}
```

`details { open: '' }` doesn't actually open them, but we should add a small JS snippet to force-open all `<details>` elements on print preview so collapsed content doesn't disappear in the PDF. See `interactivity.md`.

## Scroll-spy TOC (shared JS)

```javascript
(function() {
  const links = document.querySelectorAll('nav.toc a');
  if (!links.length) return;
  const sections = Array.from(links)
    .map(a => document.querySelector(a.getAttribute('href')))
    .filter(Boolean);
  function onScroll() {
    const y = window.scrollY + 100;
    let current = sections[0];
    for (const s of sections) { if (s.offsetTop <= y) current = s; }
    links.forEach(a => a.classList.toggle('active',
      a.getAttribute('href') === '#' + (current?.id || '')));
  }
  window.addEventListener('scroll', onScroll, { passive: true });
  onScroll();
})();
```
