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
