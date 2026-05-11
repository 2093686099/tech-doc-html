# Theme: editorial

Inspired by editorial publications, technical books, and design reports. Serif body for reading flow, single accent, generous whitespace, narrow text column. Used for documents that are primarily *read* rather than *consulted*.

**Suits:** PRD, RFC, ADR, design doc, architecture (mostly), postmortem narrative, research report.

**Doesn't suit:** execution plan, roadmap, status dashboard (use `dashboard` theme).

## Token overrides

```css
:root {
  /* Surfaces */
  --bg:          #FBFAF7;   /* warm off-white */
  --bg-elevated: #FFFFFF;   /* callouts, cards */
  --bg-code:     #F4F2EC;

  /* Text */
  --ink:        #1A1A1A;
  --ink-muted:  #5A5A5A;
  --ink-faint:  #8A8A8A;

  /* Lines */
  --rule:        #E5E2DA;
  --rule-strong: #C8C4B8;

  /* Accent default */
  --accent:      #B85C38;   /* terracotta — override per doc type */
  --accent-soft: #F4E8E0;

  /* Type sizes */
  --fs-h1:    2.25rem;
  --fs-h2:    1.5rem;
  --fs-h3:    1.175rem;
  --fs-h4:    1rem;
  --fs-body:  1.0625rem;  /* 17px */
  --fs-small: 0.875rem;
  --fs-tiny:  0.75rem;
  --lh-tight: 1.2;
  --lh-body:  1.65;

  /* Layout */
  --content-max:       720px;
  --content-max-wide:  960px;  /* for diagrams/tables */
  --nav-width:         220px;
  --gutter:            var(--space-12);
}

html[lang^="zh"], html[lang^="ja"], html[lang^="ko"] { --lh-body: 1.85; }
```

## Type roles

- Body, blockquotes: `--font-serif`
- H2, H3: `--font-serif` (weight 600, slightly tighter line-height)
- H1: `--font-sans` (weight 700, letter-spacing -0.02em) — H1 is a "label" for the doc
- H4: `--font-sans` (uppercase, letter-spacing 0.06em) — used for sub-section labels and callout headers
- Metadata, TOC, table headers, callout labels: `--font-sans`
- Code: `--font-mono`

## Layout

Desktop: `[TOC 220px] [gutter 48px] [content 720px] [right margin auto]` centered to max-width 1400px.

Mobile (<=768px): single column, `<details>` TOC at top, full-width content.

H2 sections separated by `--space-12` above and `--space-4` below. Major sections can use a `<hr class="rule-strong">` between them for stronger visual breaks.

## Editorial-specific patterns

### Subtitle under H1

```html
<header class="doc-header">
  <h1>Document Title</h1>
  <p class="doc-subtitle">One-line italic subtitle pulled from first paragraph.</p>
  <div class="meta-row">...</div>
</header>
```
```css
.doc-header { padding-bottom: var(--space-8); margin-bottom: var(--space-12);
              border-bottom: 1px solid var(--rule-strong); }
.doc-subtitle { font-size: 1.25rem; font-style: italic; color: var(--ink-muted);
                margin: 0 0 var(--space-6) 0; max-width: 36em;
                font-family: var(--font-serif); }
```

### TL;DR callout at top

If the source has a "Problem", "Motivation", "Why", or summary first paragraph, lift it into a `.callout` block right after the doc header. Use the label "TL;DR" or "SUMMARY".

### Paragraph rhythm

- `<p>` with `margin: 0 0 var(--space-4) 0`
- First paragraph after a heading: no top margin
- Blockquotes: italic, indented `--space-6`, with a subtle left border

```css
blockquote { font-style: italic; color: var(--ink-muted);
             padding-left: var(--space-6); border-left: 2px solid var(--rule);
             margin: var(--space-6) 0; }
```

### Tables

Horizontal lines only. Header row uses sans + uppercase + tiny size + muted color. Body in serif body size.

```css
table { width: 100%; border-collapse: collapse; margin: var(--space-6) 0;
        font-size: var(--fs-small); }
th, td { padding: var(--space-3) var(--space-4); text-align: left;
         border-bottom: 1px solid var(--rule); vertical-align: top; }
th { font-family: var(--font-sans); font-weight: 600; font-size: var(--fs-tiny);
     text-transform: uppercase; letter-spacing: 0.06em; color: var(--ink-muted);
     border-bottom: 1px solid var(--rule-strong); }
table.two-col th { width: 50%; }
```

### Story / persona cards (PRD-specific, but reusable)

```css
.story-grid { display: grid; grid-template-columns: 1fr 1fr; gap: var(--space-6);
              margin: var(--space-6) 0; }
.story { padding: var(--space-6); background: var(--bg-elevated);
         border: 1px solid var(--rule); }
.story-persona { font-family: var(--font-sans); font-size: var(--fs-tiny);
                 letter-spacing: 0.08em; color: var(--ink-muted);
                 margin-bottom: var(--space-3); text-transform: uppercase; }
.story-body { font-style: italic; margin-bottom: var(--space-4); }
@media (max-width: 768px) { .story-grid { grid-template-columns: 1fr; } }
```

### What editorial theme does NOT do

- No status pills with colored backgrounds — text-only status indicators
- No multi-column dashboards in the body
- No filter bars or search boxes
- No collapsible task lists (use prose with proper structure)
- No bright semantic colors except the single accent and the (rare) warning callout

If a document needs any of these, switch to dashboard theme instead.
