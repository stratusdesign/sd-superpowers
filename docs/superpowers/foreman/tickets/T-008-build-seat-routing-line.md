# T-008 — using-superpowers seat routing line (plan Task 2)

Status: review · Deliverable: #1 · Owner: builder

## Spec reference
Spec A (approved): docs/superpowers/specs/2026-09-08-methodology-roles-update-design.md
Plan (authoritative detail): docs/superpowers/plans/2026-09-08-methodology-roles-update.md — Task 2

## Scope
Execute plan Task 2 exactly as written, all steps in order.

## Acceptance criteria
- Exactly one line added in ## Skill Priority; no other diff in the file.
- Seat check and no-seat check pass (plan §standard check).
- Committed.

## Builder Report

**Ticket:** T-008
**Status:** review
**Branch:** foreman-role-concept
**Commit:** 49e08b0
**Summary:** One routing line added to using-superpowers Skill Priority; no other diff in the
  file. G2b: seat brief -> single Skill:role-cast call -> correct prohibitions. Controls clean.
**Deviations from spec:** Routing on the subagent route works via the skill's own description
  trigger (subagents damp using-superpowers auto-invocation — recorded route caveat); the line
  serves full interactive sessions.
**Known issues:** none beyond the recorded observations in the evidence file.

## Receipts
- 2026-09-09T10:30:00Z · spec-review · sa-reviewer · wrap-up-review-PASS (T-007..T-011 round 1; T-012 after corrections + micro-verify) · df12400
