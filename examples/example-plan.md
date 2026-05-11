---
title: Postgres 13 → 16 migration plan
owner: K. Rivera
target_ship: 2026-07-15
prd: ./example-prd.md
updated: 2026-05-11
---

# Postgres 13 → 16 migration plan

Move the primary application database from Postgres 13 to Postgres 16 with zero data loss, sub-minute write downtime, and a tested rollback path.

## Status

- Phase: Phase 2 of 4 — Dual-write
- Done: 6 / 15 tasks (40%)
- In progress: 2
- Blocked: 1
- Pending: 6

Next up: 2.3, 2.4. Blocked: 2.5 (waiting on extension compatibility report from `pg_stat_statements`).

## Phases

### Phase 1 — Pre-migration

These tasks shape the cutover plan and have to land before any production traffic touches a v16 node.

| ID | Task | Owner | Status | Target | Depends on |
|----|------|-------|--------|--------|------------|
| 1.1 | Audit extensions and validate v16 compatibility | K. Rivera | done | 2026-04-22 | — |
| 1.2 | Capture baseline query plans and latency P95 | K. Rivera | done | 2026-04-25 | 1.1 |
| 1.3 | Provision v16 replica in staging from v13 dump | M. Okafor | done | 2026-04-29 | 1.1 |
| 1.4 | Diff v13 vs v16 query plans, flag regressions | M. Okafor | done | 2026-05-03 | 1.2, 1.3 |

### Phase 2 — Dual-write

Application writes to both v13 and v16 simultaneously. Reads stay on v13. This is where divergence shows up if it exists; we want to catch it here, not after cutover.

| ID | Task | Owner | Status | Target | Depends on |
|----|------|-------|--------|--------|------------|
| 2.1 | Implement dual-write adapter in the data layer | K. Rivera | done | 2026-05-04 | 1.4 |
| 2.2 | Stand up logical replication v13 → v16 | M. Okafor | done | 2026-05-06 | 1.3 |
| 2.3 | Reconciliation job: row-count and checksum diff | K. Rivera | in-progress | 2026-05-13 | 2.1, 2.2 |
| 2.4 | Production canary: dual-write for 5% of write traffic | M. Okafor | in-progress | 2026-05-15 | 2.3 |
| 2.5 | Extension-compat retest under canary load | M. Okafor | blocked | 2026-05-18 | 2.4 |

### Phase 3 — Cutover

Single write window. Promote v16 to primary, retire v13 dual-write, keep v13 as warm replica for rollback.

| ID | Task | Owner | Status | Target | Depends on |
|----|------|-------|--------|--------|------------|
| 3.1 | Final reconciliation snapshot, sign-off | K. Rivera | pending | 2026-06-19 | 2.5 |
| 3.2 | Cutover window: stop writes, promote v16 | K. Rivera | pending | 2026-06-20 | 3.1 |
| 3.3 | Burn-in: 7 days reads + writes on v16 | M. Okafor | pending | 2026-06-27 | 3.2 |

### Phase 4 — Decommission

Retire v13 once the burn-in window passes without incident.

| ID | Task | Owner | Status | Target | Depends on |
|----|------|-------|--------|--------|------------|
| 4.1 | Tear down v13 replica, archive final snapshot | M. Okafor | pending | 2026-07-04 | 3.3 |
| 4.2 | Remove dual-write adapter from data layer | K. Rivera | pending | 2026-07-11 | 4.1 |
| 4.3 | Post-migration retro and runbook update | K. Rivera | pending | 2026-07-15 | 4.2 |

## Milestones

- May 15 — Production canary at 5% dual-write
- Jun 20 — Cutover window
- Jun 27 — Burn-in complete
- Jul 15 — Migration officially closed

## Risks

- **Silent data divergence under dual-write** — if reconciliation doesn't catch a write that landed in v13 but not v16, cutover will surface it as missing rows. Mitigation: reconciliation job runs every 15 min and pages on any non-zero diff.
- **Query plan regressions on v16** — the planner is not the same; some queries that were fast on v13 will be slow on v16. Mitigation: diff baseline captured in Phase 1, regressions flagged before canary.
- **Rollback complexity if cutover fails mid-window** — partial promotion can leave the cluster in a state where rolling back to v13 loses recent writes. Mitigation: rollback procedure rehearsed in staging, with an explicit no-go threshold at the 5-minute mark.
- **Extension behavior under load** — extensions that pass smoke tests may behave differently under production traffic shape. Mitigation: Phase 2.5 explicitly retests under canary load before cutover.
