# Foreman Project — Backlog

> Index only — detail lives in `tickets/`. Process in `PROCESS.md`. Concept:
> `docs/superpowers/foreman-role-concept.md` (canonical, reviewed, operator-approved structure).

## Phase 1 — Design (current)

| ID | Title | Deliverable | Status |
|---|---|---|---|
| T-001 | Spec A: sd-superpowers methodology update (role cast, foreman role, process changes) | #1 | done |
| T-002 | Spec C: documentation + ticketing standard (shared schema, PulseMark-derived) | #1 | done |
| T-003 | Spec B: orchestration harness v1 (foreman runtime: spawn, check-moments, escalation, MCP, Hermes) | #3 | done |
| T-004 | Experiment: session takeover/resume of an existing Claude session (feeds handoff design in T-003) | #3 | done |
| T-005 | Experiment: superpowers bootstrap in Happier-daemon-spawned sessions (T-001 boot contract + T-003 spawn design depend on it) | #1,#3 | done |

**Order:** T-004 → T-001 ∥ T-002 (shared boundary reconciled first) → T-005 → T-003.

## Phase 2 — Plans (after specs approved)

- Plan A (Spec A + Spec C's skill vehicle): `docs/superpowers/plans/2026-09-08-methodology-roles-update.md`
  — done (2 Codex review rounds applied; testing path corrected after Quorum overreach); tickets T-006..T-012 generated.
- Plan B (Spec B, harness repo): blocked on M0 repo creation (operator).

## Phase 3 — Build (sd-superpowers, from Plan A)

| ID | Title | Deliverable | Status |
|---|---|---|---|
| T-006 | Behavioral RED baselines in fresh sessions (plan Task 0) | #1 | done |
| T-007 | role-cast skill (plan Task 1) | #1 | done |
| T-008 | using-superpowers seat routing line (plan Task 2) | #1 | done |
| T-009 | ticket-discipline skill (plan Task 3) | #1 | done |
| T-010 | Foreman-present gates: brainstorming/writing-plans/SDD (plan Task 4) | #1 | done |
| T-011 | requesting-code-review foreman routing (plan Task 5) | #1 | done |
| T-012 | Behavioral GREEN/REFACTOR verification (plan Task 6) | #1 | done |

**Order:** strictly T-006 → T-007 → T-008 → T-009 → T-010 → T-011 → T-012 (RED baselines first; one builder at a time).

## Parked

- Mid-turn interjection test (exp. 2 tail) — nice-to-know, nothing in v1 depends on it.
- `session_permission_respond` live invocation — moot for v1 (bypass workers); revives with the
  move-off-root project.
- Move workers off root + gated permission loop (path B) — separate later project.
- Cross-project arbitration / budget-awareness — out of v1 (concept).
- Remaining concept experiments dispositioned: ring-gating checks fold into T-003 where a
  mechanism depends on a gated feature; spawn-endpoint security — covered by VPN-only posture,
  revisit at M1; relay latency/throughput — measure at M1, not before.
