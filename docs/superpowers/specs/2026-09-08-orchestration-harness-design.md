# Spec B — Orchestration Harness v1: The Foreman Runtime

> T-003 · Deliverable #3 · Status: draft v1 · pending Codex review + operator approval.
> Boundary: this spec owns the machinery (HOW the foreman runs) — spawning, driving, event
> sources, escalation transport, recovery, repo layout. Duties and prohibitions are Spec A's;
> artifact formats are Spec C's; both are referenced by name, never restated. Rationale source:
> `docs/superpowers/foreman-role-concept.md` (concept), tickets T-004/T-005 (experiment inputs).

## Goal

One foreman session supervises one project end-to-end: spawns scoped workers, observes the four
check moments, escalates to the operator's phone, and survives its own death — built only from
verified primitives, with every unverified mechanism named as an experiment (E1–E7).

## Evidence rule

Concept tags carried: VERIFIED (receipt in hand) · DECLARED (operator ground truth) ·
EXPERIMENT (E#, listed in §Experiments). Every mechanism below carries one.

## Architecture (concept topology, instantiated)

- **Foreman** = one Happier session on the host machine's daemon. Spawned by Hermes targeting
  the host daemon (VERIFIED: Hermes authenticated as second machine identity, container runs no
  daemon — concept §MCP control surface) or by the operator by hand.
- **Driving surface** = Happier MCP (`happier mcp serve`): 53-tool catalogue including
  `session_spawn_new`, `session_message_send`, `session_status_get`, `session_wait_idle`,
  `session_permission_respond` (catalogue VERIFIED; only `session_list` exercised live —
  concept §MCP control surface). Per-tool live confirmation lands with E4/E2 as each mechanism
  first uses it.
- **Workers** = Happier-daemon-spawned sessions, bypass mode via daemon-level `IS_SANDBOX=1`
  (VERIFIED on default create route — T-004/T-005; other routes E2). Layer-1 pre-action
  interception is OFF in v1 (concept consequence, operator-accepted).
- **Visibility**: sessions are account-scoped — every client (phone, web, Hermes, foreman) sees
  every session (VERIFIED: Hermes listed host sessions; dual attach live).
- **No side channels**: all agent-to-agent influence flows through the foreman or through
  artifacts (concept topology rule; duties Spec A).

## Worker lifecycle

1. **Dispatch.** Only the foreman spawns workers; one builder at a time (V1 boundary). Spawn
   carries: workdir = project root, model per PROCESS.md roles binding (Spec C), and the spawn
   brief — line 1 exactly per Spec C §Spawn brief, free-form ticket pointer after. Dispatch is
   recorded: ticket → `in-progress` plus a dispatch receipt (Spec C grammar) naming the session
   id and the effort envelope (§Check moment 4).
2. **Bootstrap.** Default daemon-create route injects the superpowers bootstrap via SessionStart
   (VERIFIED — T-005 direct JSONL receipt); role-cast is then the first skill (Spec A §Seat
   loading Path 2, satisfied). Untested routes — takeover-spawned, non-default settings, Codex
   daemon spawns (E7) — use the inline fallback: the spawn brief itself carries the role-cast
   invocation instruction (Spec A fallback; sending it is this harness's duty).
3. **Scoping (enforcement layer 2).** Ticket-derived: the worker gets only the tools/MCPs its
   ticket needs, preauthorized explicitly (T-004 lesson: bypass-enablement ≠ auto-allow —
   one refused Write observed). What the Happier spawn surface actually exposes for scoping
   (tools, MCP selection, permission mode, model, workdir) is E3; per-session MCP selection is
   VERIFIED in Happier source, the rest of the scoping surface is not yet. Until E3, scoping
   floor = workdir + model + MCP selection + spawn brief.
4. **Work.** Seat duties per Spec A; statuses and done-claims per Spec C. The foreman never
   messages content into a worker beyond dispatch, answers to raises, and gate outcomes.
5. **Completion.** Done-claim appended → `review` → foreman commissions an independent checker
   (neutral brief from artifacts + standing rubric only) → findings dispositioned per acceptance
   owners → acknowledgment → `done`. All steps are receipts (Spec C).
6. **Teardown.** Foreman stops the session; session id stays in the receipts for audit.

## Handoff: design phase → foreman (T-004 input applied)

- **Takeover is the leading candidate, not yet primary.** T-004 proved import-and-respawn with
  2-turn recall only. E1 (long session, tool results, MCP-derived fact, early/middle/recent
  recall, cwd identity, post-takeover tool+MCP work) gates promotion.
- **Until E1 passes: fresh-spawn-from-docs is the default handoff.** Docs are the durable state
  (concept invariant), so fresh-spawn is always safe.
- Decision rule: E1 pass → takeover primary, fresh-spawn fallback. E1 fail → fresh-spawn only.
  Never two co-equal production paths (T-004 acceptance criterion, honored).

## Check-moment matrix

| # | Event | Source (mechanism + tag) | Foreman response | Persisted receipt | Failure behavior | Owner |
|---|---|---|---|---|---|---|
| 1 | Work starting untied to an open ticket | Structural: workers exist only by foreman dispatch (VERIFIED spawn surface). Sweep: account session list diffed against `in-progress` tickets at every foreman wake and rehydration (VERIFIED visibility) | Unknown session → escalate (cannot attribute). Known session, unassigned work → treat as #2 silent change | Sweep receipt in foreman journal; escalation receipt on ticket if attributable | Session list unavailable → fail closed: no dispatch, no acknowledgment, escalate via fallback transport | Foreman (process); operator if plan implicated |
| 2 | Proposed change to scope / design / a ticket's definition of done | Raised: worker message to foreman (session inbox). Silent: mechanical git diff over spec files and Spec C anchors (`## Scope`, `## Acceptance criteria`, `^Status: `) at status transitions + every sweep (script layer — explicit syntax only) | Raised → route to decision-class owner (plan/scope → operator; content → sa). Silent diff without a raise → process breach: block acknowledgment, commission checker on the departure, escalate | Receipt on the ticket; journal line for spec-level diffs | Diff sweep fails → no acknowledgment can be issued (gate requires sweep) — fail closed | Operator (plan); sa (content); foreman (breach) |
| 3 | Irreversible / outside-world action | NO pre-action source in v1 (layer 1 off — concept). Compensations: layer-2 scoping (tool not granted ≠ tool misused), seat obligation to raise first (Spec A duty), Hermes post-hoc verification (VERIFIED mounts + code graph) | Raised → escalate to operator; worker holds because the foreman withholds the go-ahead (blocking = no answer, not a mechanical stop) | Escalation receipt on ticket, journal line | Escalation transport down → worker stays unanswered = blocked; fail closed | Operator |
| 4 | Rabbit-hole (one ticket consuming far more than expected) | Effort envelope set at dispatch (wall-clock + turn count, values are config not spec — E5 calibrates at M1), checked on `session_status_get` / `session_wait_idle` polls (catalogue VERIFIED; semantics E4) | Envelope breach is a signal, not a verdict → commission a scope checker (judgment, never script — concept §Script vs model); verdict routes per acceptance owners | Envelope in dispatch receipt; breach + checker receipts on ticket | Polling unavailable → sweep cadence catches at next wake; gates stay closed meanwhile | Foreman (raise); operator (if plan change) |

## Escalation transport

- **Primary: operator's phone via Happy.** The foreman session is visible and chattable on the
  phone (dual attach VERIFIED live). Whether foreman activity generates a *push* (vs. requiring
  the operator to look) plus delivery latency = **E4** — load-bearing for "phone push, worker
  blocks" (V1 scope decision).
- **Fallback: Hermes → Telegram** (concept escalation pipeline; notify path live in operator
  infra — DECLARED).
- **Blocking semantics (v1):** an open escalation freezes the decision, not the machinery — the
  affected ticket cannot transition and the worker gets no answer/acknowledgment until resolved.
  Escalations are receipts; unanswered ones survive foreman death via the journal.

## Failure and recovery (invariants concretized)

- **Durable state** = tickets, backlog, receipts, foreman journal, specs, git history. The
  foreman session holds nothing authoritative (concept invariant).
- **Foreman journal** (`foreman/journal.md`, append-only): non-ticket-scoped events — sweeps,
  spawns, escalations pending, heartbeats — as Spec C receipt-grammar lines. Ownership note:
  Spec C owns methodology artifact formats; the journal is a harness-internal artifact defined
  here, deliberately reusing Spec C's line grammar so scripts parse one syntax.
- **Fail closed, by construction:** every gated act (dispatch, acknowledgment, status
  transition) is a foreman act. Dead foreman / broken MCP / lost connection → those acts simply
  cannot happen → gated work stops. No degraded ungated mode exists to fall into.
- **Foreman death → respawn + rehydration** (normative procedure, lives in `foreman/FOREMAN.md`):
  read PROCESS.md roles binding → backlog → all non-`done` tickets → journal tail; list live
  sessions; reconcile (sweep #1). Orphan worker found: **stop it and redispatch fresh from the
  ticket** (safe: state is in artifacts); adopting a live orphan via takeover only after E1.
  Detection in v1 is operator/Hermes-initiated (manual); an automated staleness watchdog on the
  journal heartbeat is deferred to M1 observation.
- **Checker failure** = unresolved check: report, retry, or operator-approved degraded proceed —
  receipt required, never silent substitution (concept invariant carried).

## Foreman session config (thinness, mechanically)

- Its own layer-2 scoping: Happier MCP (session control) + repo read + write confined to
  tickets/backlog/journal + git commit of those paths. No code-edit tools, no builder tooling.
  Enforcement = spawn allowlist (surface per E3) + role-cast prohibitions (Spec A) + everything
  it does being receipts (audit).
- Model: holds a line rather than chasing the problem; different family from the SA preferred
  (concept routing). Binding recorded in the harness repo's PROCESS.md roles table (Spec C).

## Hermes integration

- **Spawn trigger:** Hermes spawns foremen targeting the host daemon (VERIFIED wiring;
  requester/runner recorded separately — audit distinguishes "Hermes asked" from "host ran").
- **Evidence:** Hermes verifies claims against project mounts + code graph without trusting the
  claimant (VERIFIED in operator infra). The foreman→Hermes *request channel* is unbuilt: Hermes
  runs no daemon, so it cannot host driven sessions — **E6**. Until E6: foreman-commissioned
  verification runs as fresh checker sessions with repo read access (available now); Hermes
  evidence stays operator-initiated. This respects "Hermes talks to no workers in normal
  operation" (operator-set).

## Repo layout (new harness repo — minimum to express runtime boundaries)

```
<harness-repo>/                 name = operator decision at M0
  README.md                     what it is; depends on sd-superpowers; sd-superpowers stays standalone
  PROCESS.md                    roles table binding seats to holders (Spec C format)
  backlog.md  tickets/          its own project management (Spec C schema)
  docs/STATUS.md                infra/handover state (Spec C doc standard)
  docs/experiments/             E1–E7 results, one file each
  foreman/FOREMAN.md            foreman boot + rehydration procedure (normative, §Failure)
  foreman/journal.md            append-only foreman receipts
  adapters/claude.md  adapters/codex.md   per-harness adapter status vs the concept contract
  bin/                          thin spawn/sweep wrappers over happier CLI — scripts operate on
                                explicit IDs and recorded evidence only (concept §Script vs model)
```

Everything foreman-runtime currently on branch `foreman-role-concept` migrates here at M0
(PROCESS header rule). Codex workers: supported via Happier's Codex backend (VERIFIED in source;
deep support) with inline-brief bootstrap fallback until E7; Codex layer-1 machinery
(app-server approvals) stays parked with path B.

## Experiments (all named, none gate M0)

| ID | Question | Gates |
|---|---|---|
| E1 | Takeover of a realistic session: long transcript, tool results, MCP-derived fact; early/middle/recent recall; cwd; post-takeover tool+MCP work (T-004 requirement) | Handoff primary path; orphan adoption |
| E2 | `IS_SANDBOX=1` inheritance on all spawn routes (incl. takeover) and across daemon restart | Worker spawn reliability (M1) |
| E3 | Happier spawn-scoping surface: which of tools/permission-mode/model/workdir are settable per spawn; preauthorization semantics under bypass (explicit Write preauth + non-preauth'd gated op — T-004 falsification) | Layer-2 depth; foreman thinness enforcement |
| E4 | Escalation push: does foreman/worker activity push to phone; latency; `session_wait_idle`/`session_status_get` live semantics | Escalation transport; check-moment 4 polling (M1) |
| E5 | Rabbit-hole envelope calibration (dogfood data) | Check-moment 4 thresholds (M1, config-level) |
| E6 | Foreman→Hermes evidence-request channel (Hermes hosts no daemon) | Hermes-backed checks; until then fallback per §Hermes |
| E7 | Superpowers bootstrap on Codex daemon spawns (AGENTS.md path) | Codex workers without inline fallback |

## V1 boundaries — honored

One project, one foreman; one builder at a time; workers bypass-mode (`IS_SANDBOX=1`), layer-1
interception off — v1 cannot pre-empt an unraised irreversible action, it catches it after
(concept, operator-accepted); escalation blocks the worker until answered; ambiguity =
implement-best-interpretation + deviation note (Spec A/C); no budget-awareness; no cross-project
arbitration; model-agnostic contract — nothing above assumes Claude-only workers.

## Acceptance criteria (T-003) — status

- Check-moment matrix with source, response, receipt, failure behavior, owner per event: §matrix. ✔
- Every mechanism cites a verified receipt or names an experiment: evidence tags + E1–E7 throughout. ✔
- V1 boundaries honored: §V1 boundaries. ✔
- Fail-closed + recovery invariants concretized: §Failure and recovery (construction argument,
  rehydration procedure, journal). ✔
- Codex review + operator approval: pending (this draft).
