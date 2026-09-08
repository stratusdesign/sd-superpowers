# T-003 — Spec B: orchestration harness v1

Status: open · Deliverable: #3 (new repo) · Owner: SA

## Spec reference
Concept: Topology, Enforcement (layers 2–4 active; layer 1 deferred), Check moments, Escalation,
Failure and recovery, Runtime foundation, V1 scope decisions, live-setup findings.

## Scope
The foreman runtime: spawning workers via Happier daemon (`IS_SANDBOX=1`, spawn scoping), the
foreman session's own config (Happier MCP; thinness via allowlist), check-moment event sources
(dispatch-only work-start; artifact diffs; rabbit-hole signal TBD), escalation to phone
(worker blocks), state rehydration from artifacts (disposable foreman), Hermes integration
(spawn trigger + evidence checks), repo layout for the new project.

## Acceptance criteria
- Every mechanism cites a verified receipt from the concept or names an experiment.
- V1 boundaries honored (one project, one builder, bypass workers, no budget-awareness).
- Fail-closed + recovery invariants concretized.
- Passes Codex review + operator approval.
