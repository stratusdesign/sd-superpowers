# T-012 — Behavioral GREEN/REFACTOR verification (Plan A Task 6)

Status: open · Deliverable: #1 · Owner: builder

## Spec reference
Spec A (approved): docs/superpowers/specs/2026-09-08-methodology-roles-update-design.md
Plan (authoritative detail): docs/superpowers/plans/2026-09-08-methodology-roles-update.md — Task 6
Baseline evidence: docs/superpowers/evals/2026-09-08-role-cast-results.md (T-006)

## Scope
After T-007–T-011 are complete, re-run the same R0–R7 behavioral scenarios from T-006 in fresh isolated agent/subagent sessions using existing authenticated session routes. Compare against the recorded RED/control baselines, pressure-test the discipline rules, and tighten only the new Spec A wording where a real failure is observed.

This ticket does **not** require Quorum, `superpowers-evals`, API keys, new credentials, an eval appliance, or any external testing infrastructure.

## Acceptance criteria
- R0/R1 standalone controls still pass with zero role/foreman behavior surfacing.
- R2–R7 target behaviors pass in fresh sessions against the Plan A criteria.
- Discipline behavior is pressure-tested in fresh sessions per `superpowers:writing-skills`; failures are recorded before wording is changed.
- Any wording fix is limited to the new Spec A additions; tuned pre-existing content remains untouched.
- `docs/superpowers/evals/2026-09-08-role-cast-results.md` contains before/after evidence and final verdicts for R0–R7.
- Any required wording fixes are committed and the affected scenarios re-run to GREEN.
