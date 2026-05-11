# Document type: System architecture / design doc

**Theme:** `editorial` (default). Use `dashboard` only if the doc is primarily a component inventory rather than a design rationale.

**Accent:** ink blue (default for architecture).

Audience: engineers reviewing technical decisions. They want the system at a glance first, then drill into specifics. The top-of-page overview diagram is the anchor; everything else annotates it.

## Information hierarchy

1. **Title** (H1)
2. **One-line system description** subtitle
3. **Metadata row**: owner, status (proposed/accepted/superseded), version, related ADRs/RFCs
4. **System overview diagram** — large, the first thing readers see. Mermaid `graph` or `flowchart`. This is the anchor for everything that follows.
5. **Key decisions** — 3–5 bullet callouts: "Use Postgres, not Dynamo", "Sync API only, no webhooks in V1", etc. One-line summaries with links to full ADRs below.

## Structural patterns

### System overview diagram

Prefer Mermaid. Choose orientation by component count:
- ≤6 components: `graph LR`
- >6 components: `graph TD`

Wrap in `.diagram-figure` with a `<figcaption>` giving figure number and title.

If the system has clear layers (client / API / service / data), use `subgraph` to box layers (C4-style).

### Inline SVG for architecture-specific layouts

Mermaid covers ~80% of architecture diagrams (graph / flowchart / sequenceDiagram / classDiagram / C4Context). Use Mermaid when it can express the source's diagram — auto-laid-out, theme-neutral, prints cleanly.

For the remaining 20%, use hand-tuned inline SVG. See `readability.md` Pattern 9 for the full SVG rules (when to choose SVG over Mermaid, hard rules on gradients / animation / tokens / arrow glyphs, the max-2-per-document cap). The architecture-specific triggers are:

- **Layered architecture with cross-layer annotation arrows** — Mermaid `subgraph` connectors can't carry labeled side-annotations cleanly.
- **Data-flow with parallel pipelines that share intermediate storage** — requires precise spatial relationships.
- **Network / topology diagrams** with specific geometric layout (rings, meshes, hub-and-spoke with weighted edges).
- **State machines** where Mermaid's `stateDiagram-v2` rendering doesn't communicate the intended visual model.

If the source author wrote ASCII art / box-drawing prose to express the diagram, that's a signal Mermaid wasn't enough — render as inline SVG per Pattern 9.

SVG diagrams live inside `.diagram-figure` exactly like Mermaid figures — same caption pattern, same print rules.

### Component table

After the overview diagram, list components in a table:

| Component | Responsibility | Language / Runtime | Owner |

Each row links (via `#anchor`) to a subsection below where that component is described in detail.

### Data flow — separate sequence diagrams

Don't try to show data flow on the same diagram as the system overview. Add a separate Mermaid `sequenceDiagram` for each important flow (auth, primary read path, primary write path). One diagram per flow.

### Interface contracts → code blocks

API signatures, schemas, configs: fenced code blocks with the right language tag. Quote exactly from source — don't paraphrase. Renders in `--font-mono` with `--bg-code` background.

### Alternatives considered → comparison table

If source has "We considered X, Y, Z", render as a comparison table with columns: Option, Pros, Cons, Decision.

### ADR-style decisions → callouts

Each major decision as a `.callout` with rationale visible inline:

```html
<aside class="callout">
  <div class="callout-label">Decision · ARC-007</div>
  <p><strong>Use Postgres as the primary store.</strong></p>
  <p>Rationale: transactional guarantees matter more for our workload than horizontal scaling.</p>
</aside>
```

For documents with many ADRs (>5), make each a `<details>` collapsible per `interactivity.md`.

### Non-functional requirements → metric blocks

SLA targets, throughput numbers, latency budgets in `.metric` blocks:

```html
<div class="metrics-row">
  <div class="metric">
    <div class="metric-value">99.9%</div>
    <div class="metric-label">Availability SLO</div>
  </div>
  <div class="metric">
    <div class="metric-value">&lt;150ms</div>
    <div class="metric-label">P99 latency</div>
  </div>
</div>
```

### Multiple views (logical / deployment / data / security)

If the doc has 3+ parallel views of the system, add tabs per `interactivity.md`. Each tab is one perspective with its own diagram + explanation.

If only 2 views, just put them as sequential H2 sections — tabs add overhead.

## Interactivity decisions

- **TOC** with scroll-spy: always
- **Tabs**: yes if 3+ parallel views
- **Per-ADR collapsibles**: yes if 5+ decisions documented
- **Filter**: usually no
- **Status filter on component table**: only if components have status (deprecated/stable/proposed) and there are >10

## Don'ts

- Don't redraw the source's diagrams. If source has Mermaid, render it. If source has images, embed via `<img>` with a note that the image won't be live in the single-file HTML — and ideally suggest the source author add a Mermaid version.
- Don't invent component names, responsibilities, or technical decisions the source doesn't have.
- Don't override Mermaid colors to fit the theme. Keep `theme: 'neutral'`.
- Don't add a "Future considerations" section if source doesn't have one.
