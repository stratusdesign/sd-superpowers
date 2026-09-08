# Foreman Project — Dev/Implementation Process

> The process this project runs on while building the thing that will later run the process.
> Bootstrap roles now; the foreman itself takes over process-holding once harness v0 runs
> (dogfood milestone M1). Artifacts on branch `foreman-role-concept` until the harness repo
> exists; harness material migrates there at M0.

## Roles (bootstrap phase)

| Role | Held by | Owns |
|---|---|---|
| Owner | Operator | Plan changes, approvals, escalations, acceptance |
| SA | Claude session | Specs, plans, synthesis, canonical docs |
| Independent reviewer | Codex (fresh context per review) | Adversarial review of each spec/plan/diff |
| Evidence | Hermes + repo/tests/receipts | Verification without trusting the claimant |

Rules carried from the concept: reviewed party never scopes its own review brief — neutral briefs
derive from artifacts + the standing rubric only (rubric: coherence, completeness vs cited
sections, evidence-label accuracy, over-engineering, gameable criteria). Brief commissioning:
Owner before M1 (delegable to SA strictly via the standing rubric), foreman after M1. Nothing is
real until it lands in a doc/ticket **and is acknowledged** — by the Owner before M1, by the
foreman after M1. Operator steering enters as artifacts.

## Flow (per deliverable)

spec → Codex adversarial review → synthesis → operator approval →
implementation plan (writing-plans) → tickets → build → verification → operator acceptance

- Specs: `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`
- Plans: `docs/superpowers/plans/`
- Tickets: `docs/superpowers/foreman/tickets/T-###-<slug>.md`, indexed in `backlog.md`

## Ticket standard (PulseMark-derived)

Every ticket: Status · Deliverable · Owner · Spec reference · Acceptance criteria.
Builder appends a **Report** on completion: status, branch, commit, summary, **deviations from
spec**, known issues. Ambiguous spec → implement best interpretation + deviation note (never
block); deviation notes get a review pass. Builders never edit the Spec section.

Statuses: `open` · `in-progress` · `review` · `done` · `parked`

## Milestones

- **M0** — specs A/B/C approved; harness repo created; plans written.
- **M1 (dogfood)** — harness v0 spawns one worker session end-to-end; foreman session takes over
  ticket/status-holding for this project itself.
- **M2** — v1 scope complete per concept (one project, one builder, phone escalation).
