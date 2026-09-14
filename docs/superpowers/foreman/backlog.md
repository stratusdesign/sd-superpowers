# Foreman Project — Backlog

> Index only — detail lives in `tickets/`. Process in `PROCESS.md`. Concept:
> `docs/superpowers/foreman-role-concept.md` (canonical, reviewed, operator-approved structure).

## Phase 1 — Design (current)

| ID | Title | Deliverable | Status |
|---|---|---|---|
| [T-001](tickets/T-001-spec-methodology-update.md) | Spec A: sd-superpowers methodology update (role cast, foreman role, process changes) | #1 | done |
| [T-002](tickets/T-002-spec-doc-ticketing-standard.md) | Spec C: documentation + ticketing standard (shared schema, PulseMark-derived) | #1 | done |
| [T-003](tickets/T-003-spec-orchestration-harness.md) | Spec B: orchestration harness v1 (foreman runtime: spawn, check-moments, escalation, MCP, Hermes) | #3 | done |
| [T-004](tickets/T-004-exp-session-takeover.md) | Experiment: session takeover/resume of an existing Claude session (feeds handoff design in T-003) | #3 | done |
| [T-005](tickets/T-005-exp-bootstrap-in-spawned-sessions.md) | Experiment: superpowers bootstrap in Happier-daemon-spawned sessions (T-001 boot contract + T-003 spawn design depend on it) | #1,#3 | done |

**Order:** T-004 → T-001 ∥ T-002 (shared boundary reconciled first) → T-005 → T-003.

## Phase 2 — Plans (after specs approved)

- Plan A (Spec A + Spec C's skill vehicle): `docs/superpowers/plans/2026-09-08-methodology-roles-update.md`
  — done (2 Codex review rounds applied; testing path corrected after Quorum overreach); tickets T-006..T-012 generated.
- Plan B (Spec B, harness repo): blocked on M0 repo creation (operator).

## Phase 3 — Build (sd-superpowers, from Plan A)

| ID | Title | Deliverable | Status |
|---|---|---|---|
| [T-006](tickets/T-006-build-eval-scenarios.md) | Behavioral RED baselines in fresh sessions (plan Task 0) | #1 | done |
| [T-007](tickets/T-007-build-role-cast-skill.md) | role-cast skill (plan Task 1) | #1 | done |
| [T-008](tickets/T-008-build-seat-routing-line.md) | using-superpowers seat routing line (plan Task 2) | #1 | done |
| [T-009](tickets/T-009-build-ticket-discipline-skill.md) | ticket-discipline skill (plan Task 3) | #1 | done |
| [T-010](tickets/T-010-build-foreman-gates.md) | Foreman-present gates: brainstorming/writing-plans/SDD (plan Task 4) | #1 | done |
| [T-011](tickets/T-011-build-review-routing.md) | requesting-code-review foreman routing (plan Task 5) | #1 | done |
| [T-012](tickets/T-012-build-pressure-sweep.md) | Behavioral GREEN/REFACTOR verification (plan Task 6) | #1 | done |

**Order:** strictly T-006 → T-007 → T-008 → T-009 → T-010 → T-011 → T-012 (RED baselines first; one builder at a time).

## Phase-based SA delivery — proposed 2026-09-13

Design entry: `docs/superpowers/specs/2026-09-13-phase-sa-design.md`.
Implementation/test plan: `docs/superpowers/plans/2026-09-13-phase-sa.md`.
Operator authorized design/ticketing and application of Opus findings I-1–I-5. Corrections are
applied; Sol package review confirmed them closed (spec Approved, one minor applied).
Written-package approval and acknowledgment are pending. Package receipts live in
T-013 `## Receipts`. Both PROCESS records now record the operator's bounded cross-project
foreman assignment. Startup failed before implementation; the successor SA and reviewer were
created but did not run successfully. See /home/sd-foreman/docs/experiments/2026-09-13-startup-failures.md.
New sd-foreman T-016–T-018 repair session ownership, SA binding and backend/model readiness.
sd-foreman's foreman dispatches those repairs and commissions reviews under the operator's
startup repair supervision ruling (both PROCESS files); operator keeps repair acknowledgment.
These open package tickets are not yet dispatchable.

| ID | Title | Deliverable | Status |
|---|---|---|---|
| [T-013](tickets/T-013-phase-sa-baselines.md) | Behavioral baselines for phase-based SA delivery | #1,#3 | open |
| [T-014](tickets/T-014-phase-sa-methodology.md) | Delegated phase refinement and document handover contracts | #1 | open |
| [T-015](tickets/T-015-phase-sa-verification.md) | Cross-repository phase and SA succession verification | #1,#3 | open |

Order: startup repairs sd-foreman T-016 → T-017 → T-018, then sd-superpowers T-013 → sd-superpowers T-014 → sd-foreman T-015 (rotation successor)
→ sd-superpowers T-015. Existing sd-foreman T-014 retains its approved scope and is an
additional prerequisite of sd-foreman T-015; this package does not block its dispatch.
Adoption stays gated until joint verification/integration; the SA does not dispatch these tickets.

## Parked

- Mid-turn interjection test (exp. 2 tail) — nice-to-know, nothing in v1 depends on it.
- `session_permission_respond` live invocation — moot for v1 (bypass workers); revives with the
  move-off-root project.
- Move workers off root + gated permission loop (path B) — separate later project.
- Cross-project arbitration / budget-awareness — out of v1 (concept).
- Remaining concept experiments dispositioned: ring-gating checks fold into T-003 where a
  mechanism depends on a gated feature; spawn-endpoint security — covered by VPN-only posture,
  revisit at M1; relay latency/throughput — measure at M1, not before.

## Startup repair scope reduction — 2026-09-13

Operator-approved reduced acceptance for sd-foreman T-016–T-018 now applies; T-019/T-020
are parked outside delivery scope, not dependencies. See the incident addendum's operator
scope-reduction section. Historical evidence is retained; no new implementation, ticket done
claim, or restart is authorized by these edits.
