# Document type: RFC / ADR

**Theme:** `editorial`.

**Accent:** ink blue or bronze.

RFCs (Request for Comments) and ADRs (Architecture Decision Records) are argumentative documents — they propose a change and defend the proposal. They have a fairly standard structure.

## Information hierarchy

1. **Title** with the doc ID and number (e.g. "ADR-007: Use Postgres as primary store")
2. **One-line summary** subtitle — the decision in one sentence
3. **Metadata row**: author(s), status (proposed / accepted / rejected / superseded by ADR-XXX), date
4. **TL;DR callout** with the decision and the headline rationale (1–2 sentences each)
5. Body sections follow the source's structure

## Standard structure (most ADRs/RFCs use some variant)

- **Context** — what's the problem, what's the current state
- **Decision** / **Proposal** — what we're going to do
- **Alternatives considered** — what we didn't do, and why
- **Consequences** — what this commits us to, what becomes easier, what becomes harder

## Structural patterns

### Alternatives → comparison table

If there are 2+ alternatives discussed, render as a comparison table:

| Option | Pros | Cons | Decision |
|---|---|---|---|
| Postgres | Mature, ACID | Vertical scaling limit | ✓ Chosen |
| DynamoDB | Auto-scaling | No joins | Rejected |

The "Decision" column uses `✓ Chosen` (in accent color) or `Rejected` (in muted).

### Consequences → two-column or bullet list

If source distinguishes positive/negative consequences, render as a `table.two-col` with "Trade-offs accepted" and "Capabilities gained" columns.

If it's just a flat list of consequences, render as standard bullet list.

### Decision history (for ADRs in long-lived systems)

If the ADR notes prior or superseding decisions, show as a small inline lineage:

```html
<aside class="callout">
  <div class="callout-label">Lineage</div>
  <p>Supersedes <a href="#adr-003">ADR-003</a>. Superseded by <a href="#adr-011">ADR-011</a>.</p>
</aside>
```

## Interactivity

- **TOC** with scroll-spy: always
- **Tabs**: no — RFCs read linearly
- **Collapsibles**: no, unless the source has a very long "alternatives considered" section with >4 alternatives that are independently readable
- **Filter**: no

## Don'ts

- Don't add a "Recommendation" or "Conclusion" section if source has only "Decision" — these mean different things in ADR conventions.
- Don't add status pills the source didn't have. ADRs have a controlled vocabulary: proposed / accepted / rejected / superseded. Render exactly what source says.
- Don't editorialize the alternatives. The source author chose how to present them; preserve that choice.
