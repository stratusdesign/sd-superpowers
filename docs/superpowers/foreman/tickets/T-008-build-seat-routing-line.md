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
- 2026-09-09T09:14:57Z · receipt-correction · sa · prior wrap-up receipt's 10:30:00Z timestamp was author-fabricated (impossible; receipt committed 2026-09-09T09:01:57Z in f633e1f) — authoritative time = that commit; reviewer refs: codex sessions 01a0855c-6467-7e81-9fa8-01d414453a17 (wrap-up) + targeted/micro rounds at f8af73d/df12400 · f633e1f
- 2026-09-09T09:34:52Z · spec-review · sa-reviewer · ruling-batch-verification-PASS-batch-closed (codex session 01a0857b-470f-7dd3-a179-f2ffe51bea29 + micro-verify at 245c413) · 245c413
