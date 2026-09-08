# T-003 — Spec B: orchestration harness v1

Status: done · Deliverable: #3 · Owner: sa

## Spec reference
Concept: Topology, Enforcement (layers 2–4 active; layer 1 deferred), Check moments, Escalation,
Failure and recovery, Runtime foundation, V1 scope decisions, live-setup findings.

## Scope
The foreman runtime: spawning workers via Happier daemon (`IS_SANDBOX=1`, spawn scoping), the
foreman session's own config (Happier MCP; thinness via allowlist), check-moment event sources
(dispatch-only work-start; artifact diffs; rabbit-hole signal TBD), escalation to phone
(gate progression freezes; no mechanical worker stop in v1 — wording amended with operator
approval 2026-09-08), state rehydration from artifacts (disposable foreman), Hermes integration
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
- 2026-09-08T14:26:31Z · spec-re-review · checker · 8-resolved-1-partial-1-unresolved-plus-N1-N2 · docs/superpowers/foreman/reviews/T-003-codex-review-2.md
- 2026-09-08T14:30:00Z · finding-disposition · sa · 5-and-N2-applied-8-restructured-as-spec-c-amendment-draft-v3 · docs/superpowers/specs/2026-09-08-orchestration-harness-design.md
- 2026-09-08T14:30:00Z · scope-amendment-raise · sa · T-003-scope-worker-blocks-vs-gate-freeze-to-operator · docs/superpowers/foreman/reviews/T-003-codex-review-2.md
- 2026-09-08T15:05:00Z · operator-decision · operator · journal-rejected-use-existing-artifacts · conversation-2026-09-08
- 2026-09-08T15:05:00Z · operator-decision · operator · workers-ephemeral-heartbeat-scenario-dropped · conversation-2026-09-08
- 2026-09-08T15:05:00Z · scope-amendment · operator · worker-blocks-reworded-to-gate-freeze · conversation-2026-09-08
- 2026-09-08T15:40:00Z · operator-decision · operator · simplification-pass-all-4-trims-approved · conversation-2026-09-08
- 2026-09-08T15:55:00Z · operator-review · operator · 2-findings-applied-model-precision-and-write-boundary · docs/superpowers/specs/2026-09-08-orchestration-harness-design.md
- 2026-09-08T16:05:00Z · operator-veto · operator · default-model-binding-removed-criteria-only-per-project-map · conversation-2026-09-08
- 2026-09-08T16:15:00Z · operator-veto · operator · model-criteria-removed-entirely-PROCESS-md-binding-only · conversation-2026-09-08
- 2026-09-08T16:35:00Z · operator-decision · operator · role-cast-revision-applied-to-spec-b-hermes-infrastructure-only · conversation-2026-09-08
- 2026-09-08T16:50:00Z · acknowledgment · operator · spec-b-approved-ticket-done · conversation-2026-09-08

Simplification pass (2026-09-08, operator-directed): all four trims approved and applied in
draft v5 — standing rules-tampering watcher removed (signoff review asks the question);
experiments cut to load-bearing five (E1–E4, E8); rabbit-hole envelope replaced by foreman
judgment at check-in; harness repo layout reduced to five files. Operator decisions on the
three open items also applied in v4 (journal rejected; workers ephemeral; Scope wording
amended). Awaiting operator approval.

Re-review (2026-09-08, round 2): 8/10 resolved; finding 5 (diff-gate determinism) completed in
draft v3 (working-tree sweep + per-file authorization match); finding 8 resolved by submitting
a concrete Spec C amendment for operator approval instead of a flag; new N1 (spec vs this
ticket's Scope wording "worker blocks") raised to operator as spec open item 3 — sa does not
amend its own Scope unraised; new N2 (overbroad "leaks to") applied. Verbatim:
docs/superpowers/foreman/reviews/T-003-codex-review-2.md. Awaiting operator approval — three
open items listed in the spec.
