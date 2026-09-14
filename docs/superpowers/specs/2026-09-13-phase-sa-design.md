# Phase-based SA design and delivery

Status: draft; Opus findings I-1–I-5 corrected; Sol package review 2026-09-13: spec Approved,
plan/tickets one minor (F-1, applied). Startup addendum R8–R10 tickets passed Sol review;
Project stopped after scope audit. Operator approved reduced T-016–T-018 acceptance and
removal of T-019/T-020 from delivery scope; prior reviews do not certify these revisions.
Written-spec approval pending; phase implementation has not run.
Authoring authority: operator, 2026-09-13: “OK as SA. design and ticket these changes”.
This authorizes the design and tickets; it does not claim approval of this written specification.

## Purpose

The initial SA produces a thorough project design that lets fresh phase SAs work without
repeatedly involving the operator. Each phase SA refines the design, creates tickets and a
test plan, supports builders and dispositions findings through remediation. The operator
returns for significant changes, not routine implementation decisions.

## Read this package

| Document | Owns |
|---|---|
| This file | Intent, actors, delegated authority, project document contract |
| [Startup incident and repair contracts](/home/sd-foreman/docs/experiments/2026-09-13-startup-failures.md) | Observed failed takeover; R8–R10 ownership, seat binding and launch readiness; S11–S13 |
| [Phase and session lifecycle](2026-09-13-phase-sa/lifecycle.md) | Phase entry/exit, refinement, handover, replacement and recovery |
| [Implementation and validation plan](../plans/2026-09-13-phase-sa.md) | File changes, ticket order, behavioral scenarios and rollout gate |

Existing Specs A/B/C remain the baseline. Once approved, this package supersedes their
conflicting rules only for projects explicitly adopting phase-based SA delivery in PROCESS.md.
Existing standalone and non-adopting projects keep their present workflow. The methodology
remains usable without sd-foreman; that runtime implements session control for adopting projects.

## Evidence and choices

Inspected 2026-09-13: brainstorming/SKILL.md already allows separate sub-project cycles;
writing-plans/SKILL.md requires coverage of its whole input spec and adds tasks for uncovered
requirements. Neither defines this phase authority contract. sd-foreman T-014 records one
persistent project SA; FOREMAN.md already exempts SA from per-ticket teardown. These are
repository observations, not evidence that phase rotation has been exercised.

Options considered: retaining a single project session preserves conversational continuity but
does not provide the requested context boundaries; a fresh SA for every finding discards useful
phase context and contradicts the accepted continuity intent. Selected: one accountable SA
role, with sessions normally replaced between phases and recoverably replaced within a phase.
The operator accepted this direction in the preceding discussion. Detailed rules below are the
proposal to review, not additional operator rulings.

## Actor coverage

| Actor and surface | Responsibility and limits | Observable success |
|---|---|---|
| Operator, direct SA conversation / foreman escalation | Sets intent and delegation; retains significant decisions | Approves the initial baseline, then receives only decisions outside recorded delegation or other existing reserved approvals |
| Initial SA, project session with repository access | Authors baseline and reasons; cannot assume unspecified business intent | A fresh SA can plan a phase using artifacts without repeating discovery with the operator |
| Phase SA, fresh project session | Refines within delegation; writes tickets/test plans and dispositions; does not execute builder work or self-commission reviews | Routine refinement and remediation complete without operator consultation; significant changes escalate |
| Foreman, project session and Happier control surface | Commissions independent checks, acknowledges gates, dispatches workers and replaces SA sessions; cannot decide domain content | One authoritative SA, discoverable after restart; unresolved work survives replacement |
| Builder, assigned ticket session | Implements only assigned work; reports ambiguity/deviations under existing rules | Receives bounded scope, testable acceptance and current source references |
| SA/code reviewers, fresh commissioned sessions | Review artifacts against their standing rubrics; cannot rewrite or expand scope | Detect unauthorized decisions, missing phase coverage and incomplete handovers without requiring all future phases to be ticketed |

## R1 — Initial project baseline

The initial SA resolves the decisions needed to establish the project, including material
feasibility questions that could invalidate it. Thoroughness means usable decisions and
contracts, not predicted code for every phase. Its baseline covers:

- Outcomes, material actors, important user behavior, exclusions, constraints and observable success.
- Architecture and contracts shared across phases: relevant interfaces, data ownership,
  failure/security expectations and compatibility commitments.
- Settled decisions with reasons and meaningful rejected alternatives, so successors can
  distinguish deliberate choices from accidents. Record provenance for load-bearing claims.
- Delegated discretion and reserved decisions, using R2 below and project-specific limits.
- Ordered phase outcomes, dependencies and completion conditions; detailed implementation
  belongs to the phase that is about to run.
- Known uncertainties: what is unknown, what evidence resolves it, which phase owns it,
  and which outcomes would exceed delegation. Do not disguise unknowns as settled facts.
- A project test strategy: how overall success will be demonstrated and where integration
  or cross-phase evidence will be needed. Each phase later supplies executable detail.

Use an entry document and focused subject files for a substantial design. Each requirement
has one authoritative home and a stable ID or section reference. The entry document maps
subjects to files and identifies the approved revision/approval receipt. Phase documents link
to shared constraints and decisions instead of copying competing versions. Small related
subjects can share a file; do not impose an arbitrary file count or duplicate a decision ledger.

The cold-start sufficiency test is: can a new SA explain the intent, preserve settled choices,
identify its authority, and prepare the next phase without making material assumptions?
Independent review checks this before initial operator approval. A summary of earlier chat
does not satisfy the test unless the decisions and evidence have authoritative artifact homes.

## R2 — Delegated refinement and significant changes

Initial operator approval covers the baseline, phase roadmap, and explicit delegation.
Thereafter, reviewed phase specifications/plans within that delegation require foreman
acknowledgment, not another operator approval. This is the narrow adoption-specific exception
to existing “operator approves specs/plans” gates, including discovery, candidate design,
written-spec and plan gates. Independent review and finding disposition still apply.

The phase SA may choose implementation details, task decomposition, test mechanisms, fixes
that restore agreed behavior, and revisions of provisional technical choices expressly left
to it. It records the decision, its authority reference and evidence in the affected artifact.
It cannot expand its own delegation or treat missing intent as permission.

A change is significant if it changes an agreed outcome, material actor or important user
behavior; adds scope or removes promised scope; breaks a shared contract; changes a binding
constraint, accepted risk, budget/time commitment where one exists, phase outcome/order, or
seat/model binding; overturns a reserved decision; or requires an irreversible action or
other approval reserved by existing policy. No invented numeric thresholds.

Small code size does not make a significant decision routine. Large implementation work does
not by itself make a delegated decision significant. Cumulative small changes are checked
against the approved baseline, not only against the immediately preceding edit.

SA adjudicates content; the foreman checks that authority evidence and required gates exist.
Independent reviewers compare the actual changes, including cumulative changes, against the
approved baseline and delegation. They must challenge an incorrect “routine” label; the SA's
classification is not evidence that a change is authorized.
Unclear authority routes as a narrow question through the foreman to the operator. Escalations
state the affected decision, evidence, options, recommendation and consequences. Existing
escalation/freeze behavior remains in force; silence is never approval. Approval of an exception
updates the affected authoritative documents and receipt before dependent work proceeds.

## R3 — Adoption and limits

Adoption requires an operator-approved baseline/delegation, the methodology changes and
foreman runtime changes, and their verification. The sole adoption marker is one unquoted
line in the supervised project's own root `PROCESS.md`, with this exact prefix and separator:

```text
Phase-SA: v1 · Approval: tickets/T-000-adoption.md#receipts
```

The path above is illustrative: use the existing project ticket that holds the approval,
relative to that project's root, ending in `#receipts`. That section must contain an operator
`phase-sa-adoption` receipt citing evidence that identifies the approved baseline/delegation
revision and verified methodology/runtime revisions. No new ticket is required just to hold it.
The project root is the one named in the worker assignment, not the skill installation root.
Do not inherit this marker from a parent/default PROCESS, a quoted setup example or another
repository. All skills and runtime procedures use this same test. Absent marker means existing
behavior; a malformed, duplicate or unverifiable marker grants no phase delegation and must
be resolved before using the phase-specific approval exception.

Implement and verify in isolated checkouts first. The operator performs or explicitly assigns
integration of the reviewed commits into both live checkouts after the implementation plan's
joint verification gate. The integrating worker verifies the live revisions against the reviewed
ones. Only then does the project's foreman record the already-approved adoption marker and
acknowledgment. This plan does not authorize integration now. `PROJECT-SETUP.md` must carry
the same conditional setup rule; the persistent-SA default remains for non-adopting projects.

Do not enable a partial package through the live skill symlinks. Role bindings are not changed
by this proposal. In adopting supervised projects, foreman remains dispatcher/review commissioner;
SA remains content owner; builders perform every code fix; reviewers remain fresh and independent.

This package adds no service, database, scheduler, plugin dependency, token quota, parallel
SA ownership or new ticket status. It uses existing specs, tickets, receipts and STATUS.
Runtime behavior is proposed until the plan's live scenarios have recorded evidence.

## Review and acceptance state

- Repository-grounded design, lifecycle, implementation tickets and validation scenarios: authored.
- SA self-check: recorded in the implementation plan; not independent assurance.
- Under the operator's one-time commissioning override, Claude Opus 5 reviewed this package:
  no blockers, five important findings. The operator then instructed “Apply the recommended
  corrections”; I-1–I-5 are corrected and locally checked, not independently re-approved.
  [Review and SA checks](../foreman/reviews/2026-09-13-phase-sa-opus5.md).
- Foreman-commissioned GPT 5.6 Sol review of the corrected package (session
  cmu033vli0k21pj063e336zat, 2026-09-13): spec Approved; plan/tickets Issues Found with one
  minor (F-1, RED wording in the plan), no majors; I-1–I-5 confirmed closed. SA applied F-1.
- Package review/approval receipts live in sd-superpowers
  `docs/superpowers/foreman/tickets/T-013-phase-sa-baselines.md`, under `## Receipts`.
  Written-spec approval and acknowledgment are not claimed.
- Implementation and behavioral validation: not run by this design session.

The handoff is the complete package plus the ticket index. For building this change, the
implementation plan's per-project authority table applies. Both PROCESS records now contain
the operator's bounded cross-project assignment for this package. Its attempted startup failed;
that assignment does not authorize stopping the operator's conversation or choosing an SA model.
T-016–T-018 in sd-foreman precede another automated startup attempt. Neither repository
inherits supervision outside the recorded assignment. The SA supplies artifacts;
the authorized commissioner selects reviewers and constructs neutral briefs from standing
rubrics. The one-time Opus override is not ongoing permission to self-commission reviews.
