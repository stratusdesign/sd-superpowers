# T-006 — Behavioral RED baselines in fresh sessions (Plan A Task 0)

Status: in-progress · Deliverable: #1 · Owner: builder

## Spec reference
Spec A (approved): docs/superpowers/specs/2026-09-08-methodology-roles-update-design.md
Plan (authoritative detail): docs/superpowers/plans/2026-09-08-methodology-roles-update.md — Task 0

## Scope
Before any Spec A skill change is implemented, run the Plan A behavioral baseline scenarios in fresh isolated agent/subagent sessions using the project's already-available authenticated harness/session routes. Record observed behavior as RED/control evidence.

This ticket does **not** require Quorum, `superpowers-evals`, API keys, new credentials, an eval appliance, or any external testing infrastructure. Formal upstream Quorum evaluation is optional and outside this fork's implementation path.

## Acceptance criteria
- R0–R7 baseline scenarios from Plan A Task 0 are exercised in fresh sessions.
- R0/R1 are recorded as standalone controls; no role/foreman behavior may surface.
- For behavior-changing scenarios R2–R7, actual pre-change behavior is recorded rather than inferred. If a scenario already exhibits the target behavior, it is not falsely marked RED; narrow/replace that scenario until it tests a real pre-change gap or record that no gap exists.
- Baseline evidence is written to `docs/superpowers/evals/2026-09-08-role-cast-results.md` with scenario, prompt, observed behavior, verdict, and session/evidence reference.
- No skill files are modified under this ticket.
- Committed before T-007 starts.
