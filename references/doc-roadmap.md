# Document type: Roadmap

**Theme:** `dashboard`.

**Accent:** forest or terracotta.

Roadmaps are similar to execution plans but at higher altitude — they cover quarters, not weeks. Less status-tracking, more thematic grouping. The audience is leadership and adjacent teams, not engineers.

## Information hierarchy

1. **Title** (H1) — e.g. "2026 Product Roadmap" or "Platform Roadmap H2 2026"
2. **One-line theme** subtitle — the overall direction
3. **Metadata row**: owner, scope, last-updated
4. **Quarter strip** — visual indicator of which quarter we're in (similar to phase strip in execution-plan)
5. **Themes section** — 3–5 major themes, each with a brief description
6. **Initiatives by quarter** — the main body

## Structural patterns

### Quarter strip

Like the phase strip but with quarters:

```html
<ol class="phase-strip">
  <li class="phase phase-done">
    <div class="phase-label">Q1 2026</div>
    <div class="phase-name">Foundation</div>
    <div class="phase-dates">Jan – Mar</div>
  </li>
  <li class="phase phase-active">
    <div class="phase-label">Q2 2026</div>
    <div class="phase-name">Growth</div>
    <div class="phase-dates">Apr – Jun</div>
  </li>
  <li class="phase">
    <div class="phase-label">Q3 2026</div>
    <div class="phase-name">Scale</div>
    <div class="phase-dates">Jul – Sep</div>
  </li>
</ol>
```

### Themes / pillars

If the roadmap is organized around themes (e.g. "Reliability", "Growth", "Developer Experience"), render each as a card or as a labeled section. KPI for each theme can use `.metric` blocks.

```html
<section class="themes">
  <h2><span class="accent-bar"></span>Themes</h2>
  <div class="theme-grid">
    <article class="theme">
      <div class="theme-label">Pillar 1</div>
      <h3>Reliability</h3>
      <p>Get to 99.95% availability and reduce P99 latency by 40%.</p>
      <div class="theme-kpi">
        <span class="kpi-num">99.95%</span>
        <span class="kpi-lab">Availability target</span>
      </div>
    </article>
    <!-- more themes -->
  </div>
</section>
```

```css
.theme-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
              gap: var(--space-4); margin: var(--space-6) 0; }
.theme { padding: var(--space-6); background: var(--bg-elevated);
         border: 1px solid var(--rule); border-top: 3px solid var(--accent); }
.theme-label { font-family: var(--font-sans); font-size: var(--fs-tiny);
               text-transform: uppercase; letter-spacing: 0.08em;
               color: var(--ink-muted); margin-bottom: var(--space-2); }
.theme h3 { margin: 0 0 var(--space-3); }
.theme-kpi { margin-top: var(--space-4); padding-top: var(--space-3);
             border-top: 1px solid var(--rule); }
.kpi-num { font-family: var(--font-sans); font-size: 1.25rem; font-weight: 700;
           color: var(--accent); font-variant-numeric: tabular-nums; }
.kpi-lab { font-size: var(--fs-tiny); color: var(--ink-muted);
           text-transform: uppercase; letter-spacing: 0.04em;
           margin-left: var(--space-2); }
```

### Initiatives by quarter

Each initiative gets a row in a table OR a card in a per-quarter section. Cards work better for high-altitude roadmaps; tables for denser ones.

For card layout:

```html
<section class="quarter-block">
  <h2><span class="accent-bar"></span>Q2 2026 — Growth</h2>
  <div class="initiative-grid">
    <article class="initiative" data-theme="growth">
      <div class="ini-meta">
        <span class="ini-theme">Growth</span>
        <span class="status status-active">In progress</span>
      </div>
      <h3>Self-serve onboarding flow</h3>
      <p>Reduce time-to-first-value from 3 days to 30 minutes by removing the demo-call requirement.</p>
      <div class="ini-meta">
        <span class="ini-owner">PM: Alice</span>
        <span class="date">May 24</span>
      </div>
    </article>
  </div>
</section>
```

### Filter by theme

If the roadmap has 4+ themes and 10+ initiatives, add a filter to show only initiatives matching a selected theme. See `interactivity.md`.

## Interactivity

- **TOC** with scroll-spy: always
- **Theme filter**: yes if >10 initiatives + >3 themes
- **Status filter**: yes if statuses are diverse
- **Collapse-by-quarter**: yes for multi-year roadmaps

## Don'ts

- Don't show specific task-level detail. That belongs in execution plans, not roadmaps.
- Don't promise quarters that source marks as "tentative". Render uncertainty visibly (italic "tentative" label, muted color).
- Don't add "marketing taglines" to initiatives. Match source language exactly.
