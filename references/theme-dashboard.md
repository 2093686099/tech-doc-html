# Theme: dashboard

For documents that are *consulted* rather than *read* — execution plans, roadmaps, status reports, operations docs. Information-dense, semantic color, sans-serif, optimized for scanning and re-scanning.

**Suits:** execution plan (primary), roadmap, ops report, sprint plan, project tracker.

**Doesn't suit:** PRD, RFC, postmortem (use `editorial` theme).

## Token overrides

```css
:root {
  /* Surfaces — cooler, more neutral than editorial */
  --bg:          #FAFAF9;   /* near-white but slightly warm */
  --bg-elevated: #FFFFFF;   /* panels, cards */
  --bg-subtle:   #F4F4F2;   /* alternating rows, secondary panels */
  --bg-code:     #F0EFEC;

  /* Text */
  --ink:        #18181B;
  --ink-muted:  #52525B;
  --ink-faint:  #8B8B92;

  /* Lines */
  --rule:        #E4E4E2;
  --rule-strong: #C8C8C5;

  /* Accent default — forest is the recommended default for exec plans */
  --accent:      #2C5F3F;   /* forest */
  --accent-soft: #E0E9E3;

  /* Type sizes — slightly smaller body for density */
  --fs-h1:    1.875rem;   /* 30px, smaller than editorial */
  --fs-h2:    1.375rem;   /* 22px */
  --fs-h3:    1.0625rem;
  --fs-h4:    0.875rem;
  --fs-body:  0.9375rem;  /* 15px — denser */
  --fs-small: 0.8125rem;  /* 13px */
  --fs-tiny:  0.75rem;
  --lh-tight: 1.25;
  --lh-body:  1.55;        /* tighter than editorial */

  /* Layout — wider content area for tables */
  --content-max:       880px;
  --content-max-wide:  1120px;
  --nav-width:         200px;
  --gutter:            var(--space-8);
}
```

## Type roles

- Body, table cells, lists: `--font-sans`
- Headings: `--font-sans` (heavier weights — 600 for H2/H3, 700 for H1)
- Code, numbers, dates, IDs: `--font-mono` with `font-variant-numeric: tabular-nums`
- No serif in dashboard theme — it's purely sans + mono

## Dashboard-specific patterns

### Status glance (top of every dashboard doc)

The most important pattern. The first thing a reader sees should answer: where are we, what's next, what's blocked?

```html
<section class="status-glance">
  <div class="progress-panel">
    <div class="progress-label">Phase 2 of 4 · Build</div>
    <div class="progress-bar">
      <div class="progress-fill" style="width: 62%"></div>
    </div>
    <div class="progress-stats">
      <span class="stat"><span class="stat-num">13</span><span class="stat-lab">Done</span></span>
      <span class="stat"><span class="stat-num">5</span><span class="stat-lab">In progress</span></span>
      <span class="stat"><span class="stat-num">2</span><span class="stat-lab">Blocked</span></span>
      <span class="stat"><span class="stat-num">8</span><span class="stat-lab">Pending</span></span>
    </div>
  </div>

  <div class="next-panel">
    <h3>Next up</h3>
    <ul class="next-list">
      <li><span class="status status-pending">Pending</span> 2.3 Wire OAuth providers <span class="meta-faint">— Bob</span></li>
      <li><span class="status status-pending">Pending</span> 2.4 Session storage schema <span class="meta-faint">— Alice</span></li>
    </ul>
  </div>

  <div class="blocked-panel">
    <h3>Blocked</h3>
    <ul class="blocked-list">
      <li><span class="status status-blocked">Blocked</span> 2.5 Settings UI <span class="meta-faint">— waiting on design review</span></li>
    </ul>
  </div>
</section>
```

```css
.status-glance { display: grid; grid-template-columns: 1.4fr 1fr 1fr;
                 gap: var(--space-6); margin: var(--space-6) 0 var(--space-12);
                 padding: var(--space-6); background: var(--bg-elevated);
                 border: 1px solid var(--rule); }
.status-glance h3 { font-size: var(--fs-tiny); text-transform: uppercase;
                    letter-spacing: 0.08em; color: var(--ink-muted);
                    margin: 0 0 var(--space-3); font-weight: 600; }
.progress-label { font-family: var(--font-sans); font-size: var(--fs-small);
                  color: var(--ink-muted); margin-bottom: var(--space-2);
                  text-transform: uppercase; letter-spacing: 0.06em; }
.progress-bar { height: 6px; background: var(--bg-subtle);
                margin-bottom: var(--space-4); position: relative; }
.progress-fill { height: 100%; background: var(--accent); }
.progress-stats { display: flex; gap: var(--space-6); }
.stat { display: flex; flex-direction: column; }
.stat-num { font-family: var(--font-sans); font-size: 1.5rem; font-weight: 700;
            font-variant-numeric: tabular-nums; line-height: 1; color: var(--ink); }
.stat-lab { font-size: var(--fs-tiny); color: var(--ink-muted);
            text-transform: uppercase; letter-spacing: 0.04em; margin-top: 0.25rem; }
.next-list, .blocked-list { list-style: none; padding: 0; margin: 0;
                             font-size: var(--fs-small); }
.next-list li, .blocked-list li { margin-bottom: var(--space-2); line-height: 1.4; }
.meta-faint { color: var(--ink-faint); font-size: var(--fs-tiny); }

@media (max-width: 900px) {
  .status-glance { grid-template-columns: 1fr; }
}
```

### Phase strip

Horizontal phase indicator showing progress through major phases. Current phase visually distinct.

```html
<ol class="phase-strip">
  <li class="phase phase-done">
    <div class="phase-label">Phase 1</div>
    <div class="phase-name">Discovery</div>
    <div class="phase-dates">May 1 – May 14</div>
  </li>
  <li class="phase phase-active">
    <div class="phase-label">Phase 2</div>
    <div class="phase-name">Build</div>
    <div class="phase-dates">May 15 – Jun 26</div>
  </li>
  <li class="phase">
    <div class="phase-label">Phase 3</div>
    <div class="phase-name">Beta</div>
    <div class="phase-dates">Jun 29 – Jul 17</div>
  </li>
  <li class="phase">
    <div class="phase-label">Phase 4</div>
    <div class="phase-name">GA</div>
    <div class="phase-dates">Jul 20</div>
  </li>
</ol>
```

```css
.phase-strip { display: flex; gap: 0; list-style: none; padding: 0;
               margin: var(--space-8) 0; border-top: 1px solid var(--rule);
               border-bottom: 1px solid var(--rule);
               background: var(--bg-elevated); }
.phase { flex: 1; padding: var(--space-3) var(--space-4);
         border-right: 1px solid var(--rule);
         font-family: var(--font-sans); }
.phase:last-child { border-right: none; }
.phase-label { font-size: var(--fs-tiny); text-transform: uppercase;
               letter-spacing: 0.08em; color: var(--ink-faint); }
.phase-name { font-size: var(--fs-body); font-weight: 600;
              margin-top: var(--space-1); }
.phase-dates { font-size: var(--fs-small); color: var(--ink-muted);
               margin-top: var(--space-1); font-variant-numeric: tabular-nums; }
.phase-active { background: var(--accent-soft); border-left: 3px solid var(--accent);
                margin-left: -1px; }
.phase-active .phase-name { color: var(--ink); }
.phase-done .phase-name { color: var(--ink-muted); }
.phase-done .phase-label::after { content: " ✓"; color: var(--status-done); }
```

### Task table

The workhorse of dashboard theme. Dense, scannable, owner/status/date all visible.

```html
<table class="task-table">
  <thead>
    <tr>
      <th class="col-id">ID</th>
      <th class="col-task">Task</th>
      <th class="col-owner">Owner</th>
      <th class="col-status">Status</th>
      <th class="col-target">Target</th>
      <th class="col-deps">Depends on</th>
    </tr>
  </thead>
  <tbody>
    <tr data-status="done" data-phase="1">
      <td class="mono">1.1</td>
      <td>Research existing patterns</td>
      <td>Alice</td>
      <td><span class="status status-done">Done</span></td>
      <td class="date">May 7</td>
      <td>—</td>
    </tr>
    <!-- more rows -->
  </tbody>
</table>
```

```css
.task-table { font-size: var(--fs-small); }
.task-table th { font-size: var(--fs-tiny); }
.task-table .col-id { width: 3rem; }
.task-table .col-task { width: auto; }
.task-table .col-owner, .task-table .col-status,
.task-table .col-target { width: 7rem; white-space: nowrap; }
.task-table .col-deps { width: 12rem; color: var(--ink-muted); }
.task-table td.mono { font-family: var(--font-mono); color: var(--ink-muted); }
.task-table tbody tr:hover { background: var(--bg-subtle); }
.task-table tr[data-status="blocked"] td:nth-child(2) { color: var(--status-blocked); }
.task-table tr[data-status="done"] td:nth-child(2) { color: var(--ink-muted); }
```

Add filter buttons if `>10` rows. See `interactivity.md` for the filter JS pattern.

### Milestones list

Distinct from tasks. Milestones are dates, not work.

```html
<aside class="milestones">
  <h3>Milestones</h3>
  <ul>
    <li><span class="date">May 24</span> Auth flow merged to main</li>
    <li><span class="date">Jun 7</span> OAuth complete, internal beta opens</li>
    <li><span class="date">Jul 20</span> GA</li>
  </ul>
</aside>
```

### KPI / metric grid

For ops reports, can use a tighter grid than editorial's metric callouts:

```css
.kpi-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: var(--space-4); margin: var(--space-6) 0; }
.kpi { padding: var(--space-4) var(--space-6); background: var(--bg-elevated);
       border: 1px solid var(--rule); border-left: 3px solid var(--accent); }
.kpi-value { font-family: var(--font-sans); font-size: 1.75rem; font-weight: 700;
             font-variant-numeric: tabular-nums; line-height: 1; }
.kpi-label { font-size: var(--fs-tiny); color: var(--ink-muted);
             text-transform: uppercase; letter-spacing: 0.04em;
             margin-top: var(--space-2); }
.kpi-trend { font-size: var(--fs-small); color: var(--ink-muted);
             margin-top: var(--space-1); }
.kpi-trend.up { color: var(--status-done); }
.kpi-trend.down { color: var(--status-blocked); }
```

### What dashboard theme does NOT do

- No fancy serif body — purely sans for density and scannability
- No italic subtitles or pull-quotes — dashboard is functional, not editorial
- No `box-shadow` on panels — borders only (avoids the "SaaS dashboard mockup" look)
- No bright "success green" / "warning yellow" / "error red" pills with colored backgrounds — use semantic text color from `--status-*` tokens with geometric shape prefixes
- No icons on every label — typography and color carry the meaning
