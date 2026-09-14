# Foreman Project — Dev/Implementation Process

> The process this project runs on while building the thing that will later run the process.
> Bootstrap roles now; the foreman itself takes over process-holding once harness v0 runs
> (dogfood milestone M1). Artifacts on branch `foreman-role-concept` until the harness repo
> exists; harness material migrates there at M0.

## Roles (bootstrap phase)

| Role | Held by | Owns |
|---|---|---|
| Owner | Operator | Plan changes, approvals, escalations, acceptance |
| SA | Claude Opus 5 via Claude backend; full Happier session | Specs, plans, synthesis, canonical docs |
| SA reviewer | GPT 5.6 Sol via Codex; fresh full Happier session | Adversarial review of specs/plans/tickets |
| Code reviewer | GPT 5.6 Sol via Codex; fresh full Happier session | Adversarial review of diffs/tests/builder reports |
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

**Review applies to every done-claim, not just specs.** Experiment results, completion claims,
and their interpretations get the same independent Codex review before a ticket is marked done —
the party that ran the work never certifies its own conclusions. (Added 2026-09-08 after T-004
was self-certified — the exact conflict this project exists to remove.)

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

## Cross-project foreman assignment — phase-SA package (2026-09-13)

sd-superpowers has not reached its own M1; Owner (operator) remains the default brief
commissioner/acknowledger per the roles table above for all other work. Operator instruction,
2026-09-13 (verbatim): "For this work only, I explicitly assign you supervision of its tickets
in both /home/sd-superpowers and /home/sd-foreman." Scope is bounded to the phase-based SA
delivery package: sd-superpowers T-013, T-014, T-015 and sd-foreman T-015. The sd-foreman-bound
foreman seat (Sonnet 5, per sd-foreman PROCESS.md) acts as commissioner/dispatcher/acknowledger
for these tickets in place of the operator-default rule in
`docs/superpowers/plans/2026-09-13-phase-sa.md`'s Authority table, for this package only. No
other sd-superpowers work, role binding, or the bootstrap roles table above is changed by this
assignment. Receipt: sd-superpowers T-013 `## Receipts`.

## SA and review bindings — operator ruling, 2026-09-13

Operator instruction (verbatim): “Opus 5 as SA
Codex Sol for code review and SA review”.

| Seat | Backend | Model | Session type |
|---|---|---|---|
| sa | Claude (`claude`) | Opus 5 (`claude-opus-5`) | Full Happier session |
| code-reviewer | Codex (`codex`) | GPT 5.6 Sol (`gpt-5.6-sol`) | Fresh full Happier session per review |
| sa-reviewer | Codex (`codex`) | GPT 5.6 Sol (`gpt-5.6-sol`) | Fresh full Happier session per review |

This ruling applies to the current work in /home/sd-superpowers and /home/sd-foreman. It
supersedes the family-only SA and unspecified reviewer bindings for that work. The earlier
one-off Opus review remains historical evidence, not the source of this assignment.
Record: /home/sd-foreman/tickets/T-017-explicit-sa-binding.md#receipts.

T-018 must validate the installed launch route, explicit model propagation and effective
initial response. This binding records intent, not a successful launch or review. Startup
repairs and session-ownership gates remain in force; it does not order this conversation
to close or authorize automatic takeover now.

**Temporary all-Claude override — operator ruling, 2026-09-14:** Codex is out of usage tokens
until ~2026-09-19; all seats run Claude-family for now (code-reviewer and sa-reviewer:
Claude / `claude-opus-5`, fresh full Happier session per review; builder Sonnet 5 unchanged;
sa seat operator-assigned to the resuming Fable 5 conversation). The Sol bindings above remain
standing intent and resume when Codex is available. Full record and the T-018
review-route consequence: /home/sd-foreman/PROCESS.md#temporary-all-claude-bindings--operator-ruling-2026-09-14.

## Startup repair supervision — operator ruling, 2026-09-13

Operator instruction (verbatim): “Yes — record that bounded foreman assignment for T-016–T-018”.
The sd-foreman foreman dispatches builders and commissions independent reviews for sd-foreman
T-016–T-018, including the sd-superpowers files those tickets name, and acknowledges
intermediate artifacts. The operator keeps repair acknowledgment. Interim launch/stop limits
and the full record: /home/sd-foreman/PROCESS.md#startup-repair-supervision--operator-ruling-2026-09-13.
No other sd-superpowers work or binding changes.

## Startup scope reduction — operator ruling, 2026-09-13

The operator approved removing T-019/T-020 from delivery scope, retaining T-016's ownership
correction with proportionate evidence and no future-rotation test prerequisite, and narrowing
T-017/T-018 to explicit bindings and working startup. Canonical ruling and reduced S11–S13:
/home/sd-foreman/docs/experiments/2026-09-13-startup-failures.md#operator-scope-reduction-2026-09-13.
The tickets are amended; T-019/T-020 are parked, not done. No harness dependencies remain.
The project remains stopped: this approves scope edits, not implementation dispatch or restart.

## Bounded reset dispatch override — operator, 2026-09-14

The operator explicitly instructs this authoring session to create tickets, dispatch workers
and commission reviews contrary to normal protocol, and adjust instructions/skills when
observed drift warrants it. Applies to docs/plans/process-reset.md in sd-foreman and its
named cross-repo work, including reduced T-016–T-018 and new T-021/T-022. Prior hold is lifted
for this reset only. T-019/T-020 stay parked; phase-SA feature delivery is not restarted.
Standing model bindings and operator repair acknowledgment remain. This is no permanent
SA commissioning permission. Old sessions are not control targets.
