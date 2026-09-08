# Foreman Project — Backlog

> Index only — detail lives in `tickets/`. Process in `PROCESS.md`. Concept:
> `docs/superpowers/foreman-role-concept.md` (canonical, reviewed, operator-approved structure).

## Phase 1 — Design (current)

| ID | Title | Deliverable | Status |
|---|---|---|---|
| T-001 | Spec A: sd-superpowers methodology update (role cast, foreman role, process changes) | #1 | open |
| T-002 | Spec C: documentation + ticketing standard (shared schema, PulseMark-derived) | #1 | open |
| T-003 | Spec B: orchestration harness v1 (foreman runtime: spawn, check-moments, escalation, MCP, Hermes) | #3 | open |
| T-004 | Experiment: session takeover/resume of an existing Claude session (feeds handoff design in T-003) | #3 | open |

## Phase 2 — Plans (after specs approved)

Implementation plan per approved spec (writing-plans skill). Tickets generated from plans.

## Phase 3 — Build

Generated from Phase 2. Not enumerated yet (YAGNI).

## Parked

- Mid-turn interjection test (exp. 2 tail) — nice-to-know, nothing in v1 depends on it.
- `session_permission_respond` live invocation — moot for v1 (bypass workers); revives with the
  move-off-root project.
- Move workers off root + gated permission loop (path B) — separate later project.
- Cross-project arbitration / budget-awareness — out of v1 (concept).
