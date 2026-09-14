# T-015 — Verify delegated phases and SA succession across both repositories

Status: open · Deliverable: #1,#3 · Owner: builder

## Spec reference

docs/superpowers/specs/2026-09-13-phase-sa-design.md, R1–R3, and linked lifecycle R4–R7.
Plan: docs/superpowers/plans/2026-09-13-phase-sa.md, integrated verification and S1–S13.
Incident addendum: /home/sd-foreman/docs/experiments/2026-09-13-startup-failures.md, R8–R10.

## Scope

Dispatch gate: sd-superpowers T-014 and sd-foreman T-015 plus T-016–T-018 done with reviewed reports;
package review/approval/acknowledgment receipts are in sd-superpowers T-013 `## Receipts`.
Use the frozen T-013 fixtures against both candidate revisions in fresh isolated sessions.
The operator commissions/dispatches and acknowledges this sd-superpowers ticket unless a
foreman is explicitly assigned in this project's PROCESS. Runtime work is requested from
sd-foreman's foreman; follow the plan's authority table. Builder gathers evidence.

Update docs/phase-sa-evaluation.md and docs/phase-sa-evidence/. Runtime evidence belongs in
/home/sd-foreman/docs/experiments/2026-09-13-phase-sa-lifecycle.md, created by sd-foreman T-015.
The worker assigned by sd-foreman's foreman appends a separately dated integrated-verification
section naming both revisions; preserve earlier targeted results and append any superseding
correction with its reason. Reference exact sections/revisions from the evaluation report.
Exercise planned rotation and each recovery checkpoint against
disposable sessions; do not rotate production SA or change production PROCESS adoption here.

Use S1–S10 success conditions verbatim from the plan and S11–S13 from the incident addendum. Challenge routine-decision over-escalation,
scope creep disguised as remediation, weak initial documents, premature phase closure, missing
carryover, duplicate SA, false unknown-session escalation/orphan teardown after a spawning
ticket is done, and unauthorized self-review/dispatch. Include S3's independent-reviewer variant.
Preserve before/after evidence and explicit limits; request corrective tickets for major findings.

## Acceptance criteria

- S1–S10 and incident regressions S11–S13 cite full behavioral evidence, exact fixtures and
  source/route provenance. Protected-session authority, explicit binding and effective readiness
  must hold after the combined phase changes; original incident errors are not passing tests.
- S3 demonstrates that an independent reviewer rejects a falsely routine-labelled significant
  or cumulative change; producer-only escalation evidence cannot pass that variant.
- S7–S9 include live IDs, commands, timestamps, receipt/STATUS reconciliation, readiness and
  verified inactivity; a textual rule or simulated transcript alone cannot pass these cases.
- Repeat interrupted replacement at intent/stop/spawn checkpoints; no duplicate authoritative
  SA, lost accepted obligation or premature dispatch. A valid standing SA with a done spawning
  ticket causes neither false unknown-session escalation nor teardown; genuinely unknown
  sessions retain existing escalation. Test workers are stopped and verified.
- Standalone/non-adopting behavior and existing seat boundaries are demonstrated in fresh sessions.
- Independent evidence review and SA dispositions distinguish OBSERVED, STATIC CONTRACT,
  UNSUPPORTED and INCONCLUSIVE; unresolved mandatory cases prevent an adoption-ready verdict.
- Report names exact candidate revisions and R3's operator-assigned integration and project
  foreman adoption actions. Test R3's marker/approval lookup; no enabling before joint
  verification, reviewed integration and approved baseline/delegation. This ticket enables nothing.
- Builder Report appended, independent review complete and acknowledgment precede done.
