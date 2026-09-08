# Spec B — Orchestration Harness v1: The Foreman Runtime

> T-003 · Deliverable #3 · Status: draft v2 (post Codex review 1 — all 10 findings applied) ·
> pending targeted re-review + operator approval.
> Boundary: this spec owns the machinery (HOW the foreman runs) — spawning, driving, event
> sources, escalation transport, recovery, repo layout. Duties and prohibitions are Spec A's;
> artifact formats are Spec C's (one flagged exception: §Foreman journal). Rationale source:
> `docs/superpowers/foreman-role-concept.md` (concept), tickets T-004/T-005 (experiment inputs).

## Goal

One foreman session supervises one project end-to-end: spawns scoped workers, watches the four
check moments through their v1-observable event sources — each moment's observability limit
stated in the matrix, not glossed — escalates to the operator, and survives its own death.
Built only from verified primitives, with every unverified mechanism named as an experiment
(E1–E9).

## Evidence rule

Concept tags carried: VERIFIED (receipt in hand) · DECLARED (operator ground truth) ·
EXPERIMENT (E#, listed in §Experiments). Every mechanism below carries one.

## Architecture (concept topology, instantiated)

- **Foreman** = one Happier session on the host machine's daemon. Spawn trigger: Hermes —
  wiring VERIFIED (second machine identity authenticated, MCP entry registered, cross-account
  visibility proven by Hermes listing host sessions — concept §MCP control surface); an
  end-to-end Hermes-triggered spawn is **E9**. Until E9: the operator spawns the foreman by
  hand (needs no experiment).
- **Driving surface** = Happier MCP (`happier mcp serve`): 53-tool catalogue including
  `session_spawn_new`, `session_message_send`, `session_status_get`, `session_wait_idle`
  (catalogue VERIFIED; only `session_list` exercised live — concept §MCP control surface).
  Live semantics of every load-bearing action — spawn, message, status/wait, stop/teardown —
  are **E8** (shakedown), not assumed.
- **Workers** = Happier-daemon-spawned sessions, bypass mode via daemon-level `IS_SANDBOX=1`
  (VERIFIED on default create route — T-004/T-005; other routes + daemon restart **E2**).
  Layer-1 pre-action interception is OFF in v1 (concept consequence, operator-accepted).
- **Visibility**: sessions are account-scoped — every client (phone, web, Hermes, foreman) sees
  every session (VERIFIED: Hermes listed host sessions; dual attach live).
- **No side channels**: all agent-to-agent influence flows through the foreman or through
  artifacts (concept topology rule; duties Spec A).

## Worker lifecycle

1. **Dispatch.** Only the foreman spawns workers; one builder at a time (V1 boundary). Spawn
   carries: workdir = project root, model per PROCESS.md roles binding (Spec C), and the spawn
   brief — line 1 exactly per Spec C §Spawn brief, free-form ticket pointer after. Dispatch is
   recorded: ticket → `in-progress` plus a dispatch receipt (Spec C grammar) naming the session
   id and the effort envelope (§Check moment 4). Spawn mechanics: E8.
2. **Bootstrap.** Default daemon-create route injects the superpowers bootstrap via SessionStart
   (VERIFIED — T-005 direct JSONL receipt); role-cast is then the first skill (Spec A §Seat
   loading Path 2, satisfied). Untested routes — takeover-spawned, non-default settings, Codex
   daemon spawns (**E7**) — use the inline fallback: the spawn brief itself carries the
   role-cast invocation instruction (Spec A fallback; sending it is this harness's duty).
3. **Scoping (enforcement layer 2).** Ticket-derived: the worker gets only the tools/MCPs its
   ticket needs, preauthorized explicitly (T-004 lesson: bypass-enablement ≠ auto-allow —
   one refused Write observed). What the Happier spawn surface actually exposes for scoping
   (tools, permission mode, model, workdir) is **E3**; per-session MCP selection is VERIFIED in
   Happier source, the rest of the scoping surface is not yet. Until E3, scoping floor =
   workdir + model + MCP selection + spawn brief.
4. **Work.** Seat duties per Spec A; statuses and done-claims per Spec C. The foreman never
   messages content into a worker beyond dispatch, answers to raises, and gate outcomes.
5. **Completion.** Done-claim appended → `review` → foreman commissions an independent checker
   (neutral brief from artifacts + standing rubric only) → findings dispositioned per acceptance
   owners → acknowledgment → `done`. All steps are receipts (Spec C).
6. **Teardown.** Foreman stops the session (`happier session stop` exists — VERIFIED from
   source/docs; live semantics E8); session id stays in the receipts for audit.

## Handoff: design phase → foreman (T-004 input applied)

- **Takeover is the leading candidate, not yet primary.** T-004 proved import-and-respawn with
  2-turn recall only. **E1** (long session, tool results, MCP-derived fact, early/middle/recent
  recall, cwd identity, post-takeover tool+MCP work) gates promotion.
- **Until E1 passes: fresh-spawn-from-docs is the default handoff and default recovery path** —
  safe under its preconditions, which are part of the procedure, not assumed: authoritative
  artifacts current and committed; bootstrap/brief applied on the new session (per lifecycle
  step 2); any in-flight uncommitted worker state deliberately discarded, the discard recorded
  as a receipt.
- Decision rule: E1 pass → takeover primary, fresh-spawn fallback. E1 fail → fresh-spawn only.
  Never two co-equal production paths (T-004 acceptance criterion, honored).

## Check-moment matrix

Each row states what its source can actually observe; limits are part of the row, not caveats
elsewhere.

| # | Event | Source (mechanism + tag) | Foreman response | Persisted receipt | Failure behavior | Owner |
|---|---|---|---|---|---|---|
| 1 | A session doing work not tied to a valid assigned ticket in `in-progress` | Structural: workers exist only by foreman dispatch. Sweep: account session list diffed against dispatch receipts at every wake + rehydration (visibility VERIFIED). **Observes session identity only** — it catches unknown/unregistered sessions. A known session doing unrelated work inside itself is NOT observable here; that leaks to #2's diff gate, #4's envelope, and the completion checker. The concept's work-start/heartbeat contract is deliberately not built in v1 — deferred to M1 with dogfood data (open item) | Unknown session → escalate to operator (cannot attribute) | Sweep line in foreman journal; escalation receipt | Session list unavailable → fail closed: no dispatch, no acknowledgment; escalation per §Escalation availability | Foreman (process); operator if plan implicated |
| 2 | Change to scope / design / a ticket's definition of done (incl. unauthorized status transitions) | Raised: worker message to foreman. Silent: deterministic diff gate — **baseline = the commit hash recorded in the most recent acknowledgment receipt** (Spec C receipts carry commit refs); sweep = `git diff <baseline>..HEAD` over spec files (entire) and ticket `## Scope` / `## Acceptance criteria` sections + `Status:` headers; **every hunk touching a protected range must match a raise receipt referencing it; unmatched hunk = breach flag** (mechanical — scripts flag, never interpret). Whether a matched change does what the raise said = checker judgment (concept §Script vs model) | Raised → route to decision-class owner (plan/scope → operator; content → sa). Unmatched hunk → process breach: block acknowledgment, commission checker on the departure, escalate | Receipt on the ticket; journal line for spec-level diffs | Diff sweep fails → no acknowledgment can be issued (gate requires sweep) — fail closed | Operator (plan); sa (content); foreman (breach) |
| 3 | Irreversible / outside-world action | **Source = the raised event only** (worker's raise message; raising is a Spec A seat duty). **Unraised instances are unobservable pre-action in v1** — layer 1 is off; this is the concept's stated, operator-accepted consequence ("the one thing v1 cannot do is pre-empt"). Compensations reduce exposure but do not observe: layer-2 scoping (tool not granted ≠ tool misused), Hermes post-hoc verification (VERIFIED mounts + code graph) | Raised → escalate to operator; **gate progression freezes** (no acknowledgment, no transition, no further dispatch). No mechanical worker stop is claimed; stop candidate = session stop (E8), not relied on | Escalation receipt on ticket, journal line | Escalation transport degraded → gates stay frozen; §Escalation availability applies | Operator |
| 4 | Rabbit-hole (one ticket consuming far more than expected) | Effort envelope set at dispatch (wall-clock + turn count; values are config not spec — **E5** calibrates at M1), checked on `session_status_get` / `session_wait_idle` polls (catalogue VERIFIED; live semantics **E4/E8**) | Envelope breach is a signal, not a verdict → commission a scope checker (judgment, never script); verdict routes per acceptance owners | Envelope in dispatch receipt; breach + checker receipts | Polling unavailable → sweep cadence catches at next wake; gates stay closed meanwhile | Foreman (raise); operator (if plan change) |

## Escalation transport

- **Primary: operator's phone via Happy.** The foreman session is visible and chattable on the
  phone (dual attach VERIFIED live). Whether foreman activity generates a *push* (vs. requiring
  the operator to look) plus delivery latency = **E4** — load-bearing for the V1 "phone push"
  decision.
- **Fallback: Hermes → Telegram — operator/Hermes-initiated until E6.** The notify path is live
  in operator infra (DECLARED), but the foreman cannot invoke it today: Hermes hosts no daemon,
  so no foreman→Hermes request channel exists (**E6**). What IS available when the driving
  surface is down: the foreman writes the escalation into its own session and journal — both
  visible on phone/web — and the operator (or the operator asking Hermes) closes the loop.
  Automatic fallback dispatch exists only after E6 passes.
- **Blocking semantics (v1) — gate-blocking, not execution-stopping.** An open escalation
  freezes gate progression: the affected ticket cannot transition, no acknowledgment is issued,
  no further dispatch happens, and the worker gets no answer. It does **not** mechanically stop
  a running bypass worker — v1 has no pre-action stop (matrix #3); holding after a raise is the
  worker's seat duty (Spec A), and the machinery's guarantee is the gate alone. Escalations are
  receipts; open ones survive foreman death via the journal.

## Failure and recovery (invariants concretized)

- **Durable state** = tickets, backlog, receipts, foreman journal, specs, git history. The
  foreman session holds nothing authoritative (concept invariant).
- **Fail closed — scoped to what is actually enforced:** every gated act (dispatch,
  acknowledgment, status transition, answering a raise) is a foreman act. Dead foreman, broken
  MCP, lost connection → those acts cannot happen → **ticket/gate progression stops, by
  construction; there is no degraded ungated mode for gates.** A live bypass worker mid-task is
  NOT stopped by foreman death — it runs its current work to completion and then waits
  unanswered (same v1 limit as matrix #3).
- **Foreman death → respawn + rehydration** (normative procedure, lives in `foreman/FOREMAN.md`):
  read PROCESS.md roles binding → backlog → all non-`done` tickets → journal tail; list live
  sessions; reconcile (matrix #1 sweep). Orphan worker found: **stop it and redispatch fresh
  from the ticket** under the fresh-spawn preconditions (§Handoff); adopting a live orphan via
  takeover only after E1. Detection in v1 is operator/Hermes-initiated (manual); an automated
  staleness watchdog on the journal heartbeat is deferred to M1 observation.
- **Checker failure** = unresolved check: report, retry, or operator-approved degraded proceed —
  receipt required, never silent substitution (concept invariant carried).

### Foreman journal (flagged ownership exception)

`foreman/journal.md`, append-only: non-ticket-scoped events — sweeps, spawns, pending
escalations, heartbeats — written as Spec C receipt-grammar lines verbatim, so scripts parse one
syntax. **Boundary flag for operator approval:** Spec C owns artifact formats and currently
defines receipts as ticket-scoped only; the journal is a harness-internal artifact defined
here. Operator picks at approval: (a) accept this narrow exception as-is, or (b) amend approved
Spec C to house the journal schema (amending an approved spec is a plan change — operator's
call either way).

## Foreman session config (thinness, mechanically)

- Its own layer-2 scoping: Happier MCP (session control) + repo read + write confined to
  tickets/backlog/journal + git commit of those paths. No code-edit tools, no builder tooling.
  Enforcement = spawn allowlist (surface per E3) + role-cast prohibitions (Spec A) + everything
  it does being receipts (audit).
- Model: holds a line rather than chasing the problem; different family from the SA preferred
  (concept routing). Binding recorded in the harness repo's PROCESS.md roles table (Spec C).

## Hermes integration

- **Spawn trigger:** wiring VERIFIED (authentication, MCP entry, cross-visibility; requester and
  runner recorded separately — audit distinguishes "Hermes asked" from "host ran"); end-to-end
  Hermes-triggered spawn = **E9**; operator-by-hand until then.
- **Evidence:** Hermes verifies claims against project mounts + code graph without trusting the
  claimant (VERIFIED in operator infra). The foreman→Hermes *request channel* is unbuilt —
  Hermes hosts no daemon (**E6**). Until E6: foreman-commissioned verification runs as fresh
  checker sessions with repo read access (available now); Hermes evidence stays
  operator-initiated. This respects "Hermes talks to no workers in normal operation"
  (operator-set).

## Repo layout (new harness repo — minimum to express runtime boundaries)

```
<harness-repo>/                 name = operator decision at M0
  README.md                     what it is; depends on sd-superpowers; sd-superpowers stays standalone
  PROCESS.md                    roles table binding seats to holders (Spec C format)
  backlog.md  tickets/          its own project management (Spec C schema)
  docs/STATUS.md                infra/handover state (Spec C doc standard)
  docs/experiments/             E1–E9 results, one file each
  foreman/FOREMAN.md            foreman boot + rehydration procedure (normative, §Failure)
  foreman/journal.md            append-only foreman receipts (§Foreman journal)
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
| E4 | Escalation push: does foreman/worker activity push to phone; latency; `session_wait_idle`/`session_status_get` live semantics | Escalation transport; matrix #4 polling (M1) |
| E5 | Rabbit-hole envelope calibration (dogfood data) | Matrix #4 thresholds (M1, config-level) |
| E6 | Foreman→Hermes request channel (Hermes hosts no daemon) | Hermes-backed checks; automatic escalation fallback |
| E7 | Superpowers bootstrap on Codex daemon spawns (AGENTS.md path) | Codex workers without inline fallback |
| E8 | Driving-surface shakedown: live semantics of `session_spawn_new`, `session_message_send`, `session_status_get`/`session_wait_idle`, session stop/teardown, exercised end-to-end on one worker | Every runtime mechanism; M1 entry |
| E9 | Hermes-triggered foreman spawn, end-to-end on the host daemon | Hermes spawn trigger; operator-by-hand until then |

## Open items for operator approval

1. Foreman journal format ownership: narrow exception here vs. amending approved Spec C
   (§Foreman journal).
2. Matrix #1's work-start/heartbeat contract deferred to M1 — confirm the deferral (the
   concept asked for an event contract; v1 ships identity-only detection plus the #2/#4 nets).
3. Matrix #3 / blocking semantics: v1 cannot pre-empt or mechanically stop — restated from the
   concept's accepted consequence; confirm it holds now that it is spelled out as
   gate-blocking-only.

## V1 boundaries — honored

One project, one foreman; one builder at a time; workers bypass-mode (`IS_SANDBOX=1`), layer-1
interception off — v1 cannot pre-empt an unraised irreversible action, it catches it after
(concept, operator-accepted); an open escalation freezes gate progression until answered (no
mechanical worker stop — matrix #3); ambiguity = implement-best-interpretation + deviation note
(Spec A/C); no budget-awareness; no cross-project arbitration; model-agnostic contract —
nothing above assumes Claude-only workers.

## Acceptance criteria (T-003) — status

- Check-moment matrix with source, response, receipt, failure behavior, owner per event —
  including each source's observability limit: §matrix. ✔
- Every mechanism cites a verified receipt or names an experiment: evidence tags + E1–E9
  throughout (unexercised MCP actions → E8; Hermes spawn → E9). ✔
- V1 boundaries honored: §V1 boundaries. ✔
- Fail-closed + recovery invariants concretized — and scoped to what the machinery actually
  enforces (gates, not worker execution): §Failure and recovery. ✔
- Codex review round 1: 10/10 findings applied (review: `foreman/reviews/T-003-codex-review-1.md`).
  Targeted re-review + operator approval: pending.
