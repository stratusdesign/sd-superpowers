# Phase and SA session lifecycle

Part of [Phase-based SA design and delivery](../2026-09-13-phase-sa-design.md).
Status: proposed; authority, adoption and approval state are owned by that entry document.

## R4 — Phase entry and planning

1. Read the project entry document and PROCESS.md, current STATUS, applicable shared
   decisions/contracts, previous phase's handover and evidence, and relevant open tickets.
   Check repository reality and referenced revisions. History is consulted only for a specific
   question; a whole conversation is not the bootstrap requirement.
2. Establish phase outcomes and exclusions from the approved roadmap. Reconcile implemented
   behavior, deviations, unresolved findings and uncertainties. Record design refinements
   under R2; preserve the baseline unless an authorized amendment changes it.
3. Produce the phase specification, tickets and test plan before builder dispatch. Map each
   phase success condition to tickets or verified existing capability. Project-wide requirements
   map to this phase, a named later phase, or existing verified capability. Deferral must agree
   with the roadmap and cannot move a current obligation merely to make the phase pass.
4. Include applicable shared contracts even when their implementation is in another phase.
   Check boundary compatibility and regressions in previously delivered behavior. Unavailable
   prerequisites become explicit experiment/dependency tickets, not invented working APIs.
5. Submit the artifact set to the foreman for independent review and acknowledgment. Within
   R2 delegation, this releases the phase without a new operator consultation. Significant
   changes follow the existing escalation path before dependent dispatch.

writing-plans coverage and reviewer completeness checks apply to the declared phase plus
shared constraints. They must not generate tasks for every future-phase requirement in the
project baseline. Conversely, a phase label cannot hide missing current-phase coverage.

The phase test plan names acceptance scenarios, test method/commands where known, required
fixtures/environment, expected observable results, integration/regression coverage and evidence
location. If a command depends on an experiment, name that ticket and its success condition.
Builder runs tests, independent review assesses evidence, SA dispositions content, and foreman
acknowledges completion. A claim that tests passed is not the evidence.

## R5 — Support, remediation and phase exit

Keep the current SA available through phase delivery and remediation. Minor code findings
return to builders through the foreman; major findings route to SA for corrective tickets.
Existing deviation review and immutable builder Scope/Acceptance sections remain in force.
The SA can correct its own specifications within delegation; it never edits builder code.

A corrective ticket cites its originating finding, affected requirement and acceptance test.
Restoring agreed behavior is remediation. New capabilities or weakening acceptance criteria
are not made routine by calling them remediation. Historical reports/receipts are preserved;
current documents describe reality and explicitly distinguish approved intent from deviations.

Phase exit requires evidence for its completion conditions, disposition of every finding,
current documentation, and independent review plus foreman acknowledgment. Unresolved work
must have an explicit ticket, owner, dependency impact and authorized disposition. A mandatory
acceptance failure cannot be deferred autonomously. Non-blocking carryover is allowed only
where the baseline permits it and review confirms the phase's completion conditions still hold.
Operator acceptance expressly reserved by the baseline remains reserved.

## R6 — Handover and one authoritative SA

Normal segments are initial project design, then one SA per delivery phase. A phase SA normally
stays through refinement, ticketing, support and remediation. Replace earlier when the SA or
foreman identifies context overload (repeated lost decisions, scope confusion, inability to
reconstruct obligations), or on session failure. No token threshold or phase-long session guarantee.

SA writes a focused handover in the phase's documentation; existing documents are linked,
not duplicated. It includes:

- Phase and approved baseline revision; changed decision references and reasons.
- Actual implemented state and repository revision(s), test/review evidence and known limitations.
- Open tickets/findings, pending reviews/escalations, dependencies and next permitted work.
- Required source-document reading order and any evidence that must be rechecked.

Foreman owns operational STATUS, including the authoritative SA session ID, active phase,
handover path/revision, associated assignment ticket and pending replacement step. SA owns
the content of the handover. The foreman records control events with real timestamps using
existing receipts on the replacement/phase assignment ticket; STATUS points there. Completed
ticket history remains discoverable through that pointer even when ordinary boot scans read
only non-done tickets. No second journal or new receipt grammar is introduced.

## R7 — Replacement and recovery

Apply [R8–R10 startup repair contracts](/home/sd-foreman/docs/experiments/2026-09-13-startup-failures.md) first. The stop/spawn
sequence below applies only to a verified foreman-managed SA with replacement authority and
an approved, adopted phase contract. Operator conversations remain open unless the operator
explicitly names the target and stop action; transfer delivery authority through artifacts
without terminating that conversation. Idle state is not consent. Verify the exact approved
backend/model binding and supported route before retiring a working managed SA.

1. Foreman waits for current work to reach an acknowledged checkpoint; no builder is live
   and no SA response is in flight at a planned replacement. Reconcile pending findings,
   operator messages and reviews into tickets/receipts. A phase change also requires R5 exit.
   An early replacement can inherit pending review work without declaring the phase complete.
2. Obtain the SA handover; commission independent handover sufficiency review and disposition
   findings before planned teardown. Foreman acknowledges it, records replacement intent and
   the artifact revision, then stops the outgoing SA and verifies it is inactive.
3. Only after verified inactivity, spawn a fresh full project session under existing Happier
   launch rules and explicit approved backend/model binding. Record its assignment/session ID and send the artifact
   entry point using existing spawn-brief grammar. It is the only authoritative SA.
4. Incoming SA reads R4 inputs and records a readiness response on its assignment: current
   scope, authority, unresolved obligations, next action and any inconsistency. Foreman
   acknowledges readiness only after a successful initial response, verified effective backend/model,
   and gaps dispositioned through the appropriate owner. Session creation alone is not readiness.
   Then resume routing and builder dispatch. Do not ask the operator to repeat recorded intent.

Late outputs from a retired session do not become current decisions automatically. Foreman
records and routes them to the authoritative SA for disposition. No simultaneous SA writers.

On foreman restart, reconcile STATUS's SA pointer and replacement receipts against live
sessions before generic unknown-session/orphan handling. A legitimate standing phase SA must
not be treated as unknown or orphaned merely because its spawning ticket is done: no false
escalation and no teardown. Current boot scans only non-done receipts, so this case otherwise
appears unknown (escalate, touch nothing), not an attributable orphan. If a replacement intent exists,
complete only the missing verified steps; do not spawn another SA because chat memory is empty.

On SA death, stop new dispatch, reconcile any live worker and pending output under the existing
recovery rules, and confirm the old SA is inactive before replacement. Use the latest durable
checkpoint; never invent a final handover or silently discard an accepted decision. Missing
handover content is reconstructed by the incoming SA from authoritative artifacts and independently
checked before readiness. Unknown sessions, unverifiable inactivity, inaccessible artifacts or
ambiguous ownership fail closed under existing rules. Escalate only the missing decision or
authority; elapsed time supplies neither approval nor evidence.

All start/stop/reconciliation mechanisms reuse the current runtime's documented controls.
The correctness of this sequence still requires the plan's live replacement and recovery tests.
