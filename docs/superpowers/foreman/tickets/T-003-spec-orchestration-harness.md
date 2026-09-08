# T-003 — Spec B: orchestration harness v1

Status: review · Deliverable: #3 · Owner: sa

## Spec reference
Concept: Topology, Enforcement (layers 2–4 active; layer 1 deferred), Check moments, Escalation,
Failure and recovery, Runtime foundation, V1 scope decisions, live-setup findings.

## Scope
The foreman runtime: spawning workers via Happier daemon (`IS_SANDBOX=1`, spawn scoping), the
foreman session's own config (Happier MCP; thinness via allowlist), check-moment event sources
(dispatch-only work-start; artifact diffs; rabbit-hole signal TBD), escalation to phone
(worker blocks), state rehydration from artifacts (disposable foreman), Hermes integration
(spawn trigger + evidence checks), repo layout for the new project (minimum needed to express runtime boundaries — no more).

## Acceptance criteria
- Check-moment matrix: each event → source, foreman response, persisted receipt, failure
  behavior, owner.
- Every mechanism cites a verified receipt from the concept or names an experiment.
- V1 boundaries honored (one project, one builder, bypass workers, no budget-awareness).
- Fail-closed + recovery invariants concretized.
- Passes Codex review + operator approval.

## Result

**Spec:** docs/superpowers/specs/2026-09-08-orchestration-harness-design.md

Draft v1 (2026-09-08). T-004 input applied (takeover = leading candidate gated on E1;
fresh-spawn default until then). T-005 input applied (bootstrap verified on default
daemon-create route; inline fallback retained for untested routes). Experiments E1–E7 named;
none gate M0. Codex adversarial review pending.

Review round 1 (2026-09-08): Codex, fresh context, neutral brief — 2 blockers, 6 material,
2 minor; all 10 accepted by sa and applied in draft v2 (observability limits stated per check
moment; blocking narrowed to gate progression; diff-gate baseline defined; E8/E9 added;
fallback transport narrowed; journal ownership flagged as an operator decision). Verbatim
findings: docs/superpowers/foreman/reviews/T-003-codex-review-1.md. Targeted re-review of
blocker resolutions pending, then operator approval.

## Receipts
- 2026-09-08T13:50:00Z · dispatch · operator · execute-T-003-per-PROCESS · conversation-2026-09-08
- 2026-09-08T14:19:09Z · spec-review · checker · 2-blocker-6-material-2-minor · docs/superpowers/foreman/reviews/T-003-codex-review-1.md
- 2026-09-08T14:25:00Z · finding-disposition · sa · all-10-accepted-applied-draft-v2 · docs/superpowers/specs/2026-09-08-orchestration-harness-design.md
