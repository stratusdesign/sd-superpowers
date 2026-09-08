# T-006 — Behavioral RED baselines in fresh sessions (Plan A Task 0)

Status: review · Deliverable: #1 · Owner: builder

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

## Builder Report

**Ticket:** T-006
**Status:** review
**Branch:** foreman-role-concept
**Commit:** ac7c60e
**Summary:** 8 baseline probes run as fresh isolated Haiku subagent sessions; evidence in
  docs/superpowers/evals/2026-09-08-role-cast-results.md. Controls R0/R1 pass; R3/R5/R6/R7 RED
  (R3/R5 severe); R2 contaminated, R4 invalid — both with recorded reasons and adjusted GREEN
  criteria.
**Deviations from spec:** Probes were dispatched by the controller session directly rather than
  via an intermediate builder session (cost directive). R3/R5 probes escaped their fixture dirs
  and modified the real repo (2 commits) — fully reverted, never pushed, recorded as RED
  evidence; containment rule added for all future probes (explicit directory confinement +
  isolated worktrees).
**Known issues:** R2/R4 cannot yield honest content-level baselines on a shared filesystem;
  their T-012 GREEN criteria are routing-evidence-based, per the evidence file.

