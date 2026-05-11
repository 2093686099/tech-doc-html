# Data visualization (Chart.js via CDN)

This file is for the rare case where the source MD contains tabular data or describes a chart that should be rendered as a real visualization. Most technical docs don't need this — Mermaid handles flows, diagrams, gantts, and sequence diagrams; tabular data is fine in HTML tables.

**Only use Chart.js when:**

- Source explicitly contains chart data (e.g. a `chart` code block with data, or a structured table that's clearly chart material like "Quarter | Revenue")
- User explicitly asks for a chart
- A postmortem timeline benefits from a real time-series plot
- A roadmap or research report includes trend/distribution data

**Do NOT use Chart.js for:**

- Decoration ("this report would look nicer with a chart")
- Inventing data the source doesn't have
- Status counts (use the status-glance pattern from dashboard theme instead)
- Phase progress (use the phase strip from dashboard theme instead)

## Loader

Chart.js v4 via jsdelivr CDN. Place at the bottom of the HTML, before closing `</body>`:

```html
<script src="https://cdn.jsdelivr.net/npm/chart.js@4/dist/chart.umd.min.js"></script>
```

## Color rules

Charts inherit the document's typography but use a restrained palette. **No rainbow.**

For single-series charts: use `--accent` for the series.

For multi-series (≤4 series): use a sequence from a single accent at different lightness levels, or a small editorial palette:

```javascript
const palette = ['#1F3A5F', '#5A7A8C', '#8FA0AE', '#C1C6CC']; // ink-blue progression
// or
const palette = ['#B85C38', '#D08465', '#E3A88E', '#EDC8B8']; // terracotta progression
```

Set chart defaults to match the document:

```javascript
Chart.defaults.font.family = "'Söhne', 'IBM Plex Sans', -apple-system, sans-serif";
Chart.defaults.font.size = 13;
Chart.defaults.color = '#5A5A5A';        // ink-muted
Chart.defaults.borderColor = '#E5E2DA';   // rule
Chart.defaults.plugins.legend.labels.boxWidth = 12;
Chart.defaults.plugins.legend.labels.boxHeight = 2;  // hairline, not big square
```

## Wrap charts in figures

Same `figure` pattern as Mermaid diagrams:

```html
<figure class="diagram-figure">
  <div class="chart-wrap" style="height: 320px;">
    <canvas id="chart-revenue"></canvas>
  </div>
  <figcaption>Figure 3. Revenue by quarter, 2024–2026.</figcaption>
</figure>

<script>
new Chart(document.getElementById('chart-revenue'), {
  type: 'line',
  data: { /* ... */ },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    plugins: { legend: { position: 'bottom' } },
    scales: { y: { grid: { color: '#E5E2DA' } }, x: { grid: { display: false } } }
  }
});
</script>
```

```css
.chart-wrap { position: relative; }
```

## Print handling

Chart.js renders to canvas, which prints fine but may scale awkwardly. Add to the print stylesheet:

```css
@media print {
  .chart-wrap { height: 240px !important; }
  canvas { max-width: 100% !important; height: auto !important; }
}
```

## Reject these chart types

- 3D charts of any kind
- Doughnut/pie charts with >5 slices
- Polar area, radar — almost always wrong choice
- Animated chart entrances (set `animation: false` in options)
- Gradient fills (use solid colors)

## If the source has a Mermaid chart in MD

Render it as Mermaid. Don't convert to Chart.js. Mermaid is the right tool for diagrams; Chart.js is for data plots.

## If the source has a table that could be a chart

Render BOTH the table (so the data is exact and accessible) AND the chart. The chart goes above for visual scan, the table goes below for precision. Don't replace the table with a chart — never lose data.
