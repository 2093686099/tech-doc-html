# Document type: Postmortem / retro

**Theme:** `editorial`.

**Accent:** burgundy (recommended) — the somber color signals seriousness without being alarming. Or terracotta if the postmortem is more retrospective than incident-focused.

Postmortems are difficult documents to render. They're emotionally weighted (often after something went wrong), they need to be honest, and they're reference material that gets re-read. Avoid making them look celebratory or dashboard-y.

## Information hierarchy

1. **Title** (H1) — include incident date if applicable
2. **One-line summary** subtitle — what happened, in past tense, without blame
3. **Metadata row**: author(s), incident date(s), severity, services affected, status (draft / final)
4. **Impact summary** — `.metric` blocks for hard numbers (duration, users affected, revenue impact if known)
5. **TL;DR callout** — what happened, root cause headline, what we're changing
6. Body sections follow the source structure

## Standard structure

- **Summary** — narrative overview
- **Impact** — quantified
- **Timeline** — chronological log of the incident
- **Root cause** — what actually went wrong
- **Contributing factors** — what made the impact worse or detection slower
- **What went well** — explicit recognition (postmortems should include this)
- **Action items** — concrete follow-ups, with owners

## Structural patterns

### Impact metrics

Time, users, revenue, etc. — use `.metric` blocks. Tabular numerals critical for time/duration display.

```html
<div class="metrics-row">
  <div class="metric">
    <div class="metric-value">47 min</div>
    <div class="metric-label">Time to mitigation</div>
  </div>
  <div class="metric">
    <div class="metric-value">~12k</div>
    <div class="metric-label">Users affected</div>
  </div>
</div>
```

### Timeline

Render as a structured chronological list. Each entry has a precise timestamp, who/what acted, and what happened. Use monospace for timestamps.

```html
<ol class="timeline">
  <li>
    <time class="ts">14:23 UTC</time>
    <div class="tl-body">
      <strong>Alert fires.</strong> P99 latency on api.example.com exceeds 2s threshold.
    </div>
  </li>
  <li>
    <time class="ts">14:31 UTC</time>
    <div class="tl-body">
      <strong>On-call paged.</strong> Engineer acknowledges in 4 minutes.
    </div>
  </li>
</ol>
```

```css
.timeline { list-style: none; padding: 0; margin: var(--space-6) 0;
            border-left: 1px solid var(--rule); padding-left: var(--space-6); }
.timeline li { position: relative; margin-bottom: var(--space-6); }
.timeline li::before { content: ""; position: absolute; left: calc(-1 * var(--space-6) - 4px);
                       top: 0.4em; width: 7px; height: 7px; background: var(--accent);
                       border-radius: 50%; }
.ts { font-family: var(--font-mono); font-size: var(--fs-small); color: var(--ink-muted);
      font-variant-numeric: tabular-nums; display: block; margin-bottom: var(--space-1); }
.tl-body { font-size: var(--fs-body); line-height: var(--lh-body); }
```

### Root cause and contributing factors

Render as ordinary prose sections. Don't add `.callout-warn` to root cause — too alarming. Use plain paragraphs.

For contributing factors that are independently scannable (alert tuning, runbook gaps, knowledge silos), use a normal bulleted list.

### What went well

Render as a normal section. **Don't** add green checkmarks or celebratory styling — same tone as the rest of the doc.

### Action items

A table works best:

```html
<table>
  <thead>
    <tr><th>Action</th><th>Owner</th><th>Target</th><th>Status</th></tr>
  </thead>
  <tbody>
    <tr>
      <td>Add latency-budget alert on /checkout</td>
      <td>Alice</td>
      <td><span class="date">May 24</span></td>
      <td><span class="status status-active">In progress</span></td>
    </tr>
  </tbody>
</table>
```

Action items are the most actionable part of the postmortem — keep them visible at the bottom, fully scannable.

## Interactivity

- **TOC** with scroll-spy: always
- **Collapsibles**: timeline entries can be collapsed if there are >20 — but usually keep them all visible; the timeline IS the postmortem
- **Filter on action items**: yes if >10 action items
- **Tabs**: no

## Don'ts

- Don't use language softer than the source. If source says "the database was unavailable", don't render as "there was a database hiccup".
- Don't add "blame language" or attribute fault to individuals. Match source.
- Don't add ⚠️ or 🚨 emoji to incident sections. The seriousness is in the content, not the icon.
- Don't add a "Lessons learned" section if source doesn't have one — many postmortems intentionally integrate lessons throughout.
