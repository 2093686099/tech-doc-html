# Interactivity decision guide

When to add what, with vanilla JS patterns. The skill makes these calls based on the source document; the user shouldn't have to ask for them explicitly.

## Always include (free wins)

### Scroll-spy TOC

Every document gets a sticky-on-desktop, collapsible-on-mobile TOC with active-section highlighting. This is in `core-tokens.md`. No exceptions.

### Anchor links on headings

Every `h2` and `h3` should have an `id` derived from its text. This enables Ctrl+F navigation, deep links, and the TOC.

### Print-friendly collapse expansion

If the document has any `<details>` collapsibles, add this snippet so printing doesn't lose content:

```javascript
window.addEventListener('beforeprint', () => {
  document.querySelectorAll('details').forEach(d => { d.dataset.wasOpen = d.open; d.open = true; });
});
window.addEventListener('afterprint', () => {
  document.querySelectorAll('details').forEach(d => { d.open = d.dataset.wasOpen === 'true'; });
});
```

## Conditional features — when to add each

### Status / category filter on a table

**Add when:** task table or risk table has >10 rows AND has a status (or category) column with at least 3 distinct values.

**Skip when:** table is short (<10 rows), or there's no meaningful categorization.

Pattern:
```html
<div class="filter-bar">
  <span class="filter-label">Filter:</span>
  <button class="filter-btn active" data-filter="all">All <span class="count">21</span></button>
  <button class="filter-btn" data-filter="active">In progress <span class="count">5</span></button>
  <button class="filter-btn" data-filter="blocked">Blocked <span class="count">2</span></button>
  <button class="filter-btn" data-filter="pending">Pending <span class="count">8</span></button>
  <button class="filter-btn" data-filter="done">Done <span class="count">6</span></button>
</div>

<table class="task-table">
  <!-- rows have data-status="done" | "active" | "blocked" | "pending" -->
</table>
```

```javascript
document.querySelectorAll('.filter-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    const filter = btn.dataset.filter;
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.toggle('active', b === btn));
    document.querySelectorAll('.task-table tbody tr').forEach(row => {
      row.style.display = (filter === 'all' || row.dataset.status === filter) ? '' : 'none';
    });
  });
});
```

```css
.filter-bar { display: flex; gap: var(--space-2); align-items: center; flex-wrap: wrap;
              margin: var(--space-4) 0; font-family: var(--font-sans); font-size: var(--fs-small); }
.filter-label { color: var(--ink-muted); font-size: var(--fs-tiny); text-transform: uppercase;
                letter-spacing: 0.06em; margin-right: var(--space-2); }
.filter-btn { background: none; border: 1px solid var(--rule); padding: var(--space-1) var(--space-3);
              color: var(--ink-muted); cursor: pointer; font: inherit;
              font-family: var(--font-sans); }
.filter-btn:hover { color: var(--ink); border-color: var(--rule-strong); }
.filter-btn.active { color: var(--ink); border-color: var(--ink); background: var(--bg-elevated); }
.filter-btn .count { color: var(--ink-faint); margin-left: var(--space-1);
                      font-variant-numeric: tabular-nums; font-size: var(--fs-tiny); }
.filter-btn.active .count { color: var(--ink-muted); }
```

### Collapsible sections

**Add when:** the section contains a list of 5+ similar items (FAQ, decisions/ADRs, risks with mitigations) where each item is independently readable. Use plain HTML `<details>`/`<summary>` — no JS needed.

**Skip when:** content is one continuous flow, or items are short enough that hiding adds friction.

```html
<details class="collapse-item">
  <summary>
    <span class="collapse-title">ADR-007: Use Postgres as primary store</span>
    <span class="collapse-meta">Accepted · 2026-04-12</span>
  </summary>
  <div class="collapse-body">
    <p>Rationale: ...</p>
  </div>
</details>
```

```css
details.collapse-item { border-bottom: 1px solid var(--rule); padding: var(--space-3) 0; }
details.collapse-item summary { cursor: pointer; list-style: none;
                                 display: flex; justify-content: space-between;
                                 align-items: baseline; }
details.collapse-item summary::-webkit-details-marker { display: none; }
details.collapse-item summary::before { content: "+ "; color: var(--accent);
                                         font-family: var(--font-mono);
                                         margin-right: var(--space-2); }
details.collapse-item[open] summary::before { content: "− "; }
.collapse-title { font-weight: 500; }
.collapse-meta { font-family: var(--font-sans); font-size: var(--fs-tiny);
                 color: var(--ink-faint); text-transform: uppercase;
                 letter-spacing: 0.06em; }
.collapse-body { padding: var(--space-3) 0 var(--space-2) var(--space-6);
                 font-size: var(--fs-small); }
```

### Tabs for multiple views

**Add when:** the document presents the same subject from multiple angles (architecture: logical/deployment/data/security; PRD: V1/V2; comparison: option A/B/C with parallel structure). Three or more views.

**Skip when:** only 2 views (just put them side-by-side with a comparison table). Or views are too long to fit a tab paradigm (each is its own multi-section document — keep sequential).

```html
<div class="tabs">
  <nav class="tab-nav" role="tablist">
    <button class="tab-btn active" data-tab="logical">Logical</button>
    <button class="tab-btn" data-tab="deploy">Deployment</button>
    <button class="tab-btn" data-tab="data">Data</button>
  </nav>
  <section class="tab-panel active" data-tab="logical">...</section>
  <section class="tab-panel" data-tab="deploy">...</section>
  <section class="tab-panel" data-tab="data">...</section>
</div>

<script>
document.querySelectorAll('.tab-btn').forEach(b => b.onclick = () => {
  const t = b.dataset.tab;
  b.parentElement.querySelectorAll('.tab-btn').forEach(x => x.classList.toggle('active', x.dataset.tab === t));
  b.closest('.tabs').querySelectorAll('.tab-panel').forEach(x => x.classList.toggle('active', x.dataset.tab === t));
});
</script>
```

```css
.tab-nav { display: flex; gap: var(--space-6); border-bottom: 1px solid var(--rule);
           margin: var(--space-6) 0; }
.tab-btn { background: none; border: none; padding: var(--space-3) 0;
           font-family: var(--font-sans); font-size: var(--fs-body);
           color: var(--ink-muted); cursor: pointer; border-bottom: 2px solid transparent;
           margin-bottom: -1px; }
.tab-btn.active { color: var(--ink); border-bottom-color: var(--accent); }
.tab-panel { display: none; }
.tab-panel.active { display: block; }
```

For print: expand all panels. In `core-tokens.md` print styles, override `.tab-panel { display: block; }` and add a print-only heading derived from the tab label.

### Collapse-by-phase (execution plan specific)

**Add when:** an execution plan has 4+ phases and total tasks >20. Each phase becomes a collapsible section.

**Default state:** open the current phase, collapse done phases, collapse upcoming phases. This way the developer sees what's relevant immediately.

```html
<details class="phase-section" open>
  <summary><h2>Phase 2 · Build <span class="phase-meta">5 in progress · 2 blocked</span></h2></summary>
  <!-- phase content -->
</details>
```

## Render-time data quality checks

The renderer should detect a few common authoring mistakes and surface them in the HTML rather than silently producing broken output.

### Circular task dependencies

If task dependencies form a cycle (`2.4 depends on 2.5`, `2.5 depends on 2.4`, or any longer loop), the "Next up" computation will starve — no task can ever satisfy its dependencies. Detect this by running a topological sort over the dependency edges; if a cycle is present, the sort fails.

When detected, render a red callout at the top of the document above the status glance:

```html
<aside class="callout callout-warn" style="border-left-color: var(--status-blocked);">
  <div class="callout-label" style="color: var(--status-blocked);">Dependency cycle detected</div>
  <p>Tasks <code>2.4 → 2.5 → 2.4</code> form a cycle. "Next up" cannot compute reliably. Fix the source MD before relying on this snapshot.</p>
</aside>
```

Cycles are usually typos (author wrote `2.5` when they meant `2.3`), and the callout makes the typo obvious instead of producing a misleading empty "Next up" panel.

### Dangling dependency references

If a task lists `depends: 2.9` but no task `2.9` exists in the document, surface that too — same warning callout style, listing the unknown IDs. This often means an author renumbered tasks and missed an update.

### Tasks past their target with no status change

If a task is `in-progress` (or `pending`) and its `target` date is more than 3 days in the past, auto-render it as `at-risk` in the HTML — even if the MD still says `in-progress`. Add a subtle note "(target was N days ago)" in the row. This is the "honest snapshot" rule from `doc-execution-plan.md` operationalized.

## What NOT to add

- **Search box.** Browser Ctrl+F is better. Custom search has to deal with matched indices, highlighting, jumping — easy to get wrong. Skip.
- **Sort buttons on tables.** Sorting is rarely needed; if data has a natural order (priority, phase, date), the source should encode it.
- **Tooltips on hover.** Don't hide information behind interaction. If something needs explaining, put it in the text.
- **Real-time anything.** No live data fetching, no WebSocket, no API calls. The HTML is a static snapshot of the MD at render time. Re-render to update.
- **Sticky elements other than the TOC.** No sticky filter bar, no sticky table header beyond the natural one. Pollutes the viewport.
- **Theme switcher / dark mode toggle.** Light only. User decided.
- **Persistent reader-side state.** Reader-side ephemeral state (filter selections, expanded collapsibles, current tab) is fine — it lives until page reload and doesn't claim authority over anything. **Persistent state via `localStorage` / `sessionStorage` / cookies is banned**, even for benign-looking features like "mark this section as read", "remember which filter I picked", "hide tasks I own". Three reasons: (1) the MD is source of truth — any persisted state competes for authority and creates a divergence path back into the source on re-render; (2) reader-side persisted state on a doc shared across stakeholders quickly grows into "who marked what" semantics, which is a real data store and doesn't belong in a static HTML snapshot; (3) the HTML opens from email attachments, file:// URLs, intranet shares — environments where storage scoping is unpredictable and any cross-doc leakage is a surprise. If a use case genuinely needs two-way state (annotations, review checkboxes), it's a different tool, not this skill.

## Decision summary

| Document feature | Add interactivity? |
|---|---|
| Task table, >10 rows, multiple statuses | ✓ Status filter |
| 4+ phases × 5+ tasks each | ✓ Collapse-by-phase, current open |
| 5+ similar independent items (FAQ, ADRs, risks) | ✓ Collapsible per item |
| 3+ parallel "views" of same subject | ✓ Tabs |
| Long table of options/decisions | Maybe filter, depends on column count |
| Architecture overview with 1 system diagram | ✗ Static, just render Mermaid |
| PRD narrative with metrics | ✗ Static |
| Postmortem timeline | ✗ Static |
| RFC with 1-2 alternatives discussed | ✗ Static, side-by-side comparison |
| Short doc (<5 sections) | ✗ Static |

When in doubt: lean static. Interactivity adds bugs and breaks Ctrl+F.
