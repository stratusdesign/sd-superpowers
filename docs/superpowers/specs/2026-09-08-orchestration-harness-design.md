# Spec B — Orchestration Harness v1: The Foreman Runtime

> T-003 · Deliverable #3 · Status: draft v5 (operator decisions + simplification pass
> 2026-09-08 applied) · pending operator approval.
> Boundary: this spec owns the machinery (HOW the foreman runs) — spawning, driving, event
> sources, escalation transport, recovery, repo layout. Duties and prohibitions are Spec A's;
> artifact formats are Spec C's, unchanged. Rationale source:
> `docs/superpowers/foreman-role-concept.md` (concept), tickets T-004/T-005 (experiment inputs).

## Goal

One foreman session supervises one project end-to-end: spawns scoped workers, watches the four
check moments through their v1-observable event sources — each moment's observability limit
stated in the matrix, not glossed — escalates to the operator, and survives its own death.
Built only from verified primitives, with every unverified mechanism named as an experiment
(§Experiments).

## Evidence rule

Concept tags carried: VERIFIED (receipt in hand) · DECLARED (operator ground truth) ·
EXPERIMENT (E#, listed in §Experiments). Every mechanism below carries one.

## Architecture (concept topology, instantiated)

- **Foreman** = one Happier session on the host machine's daemon. Spawn trigger: Hermes —
  wiring VERIFIED (second machine identity authenticated, MCP entry registered, cross-account
  visibility proven by Hermes listing host sessions — concept §MCP control surface); an
  in v1 the operator spawns the foreman by hand; Hermes-triggered spawning is deferred until
  a project actually needs it (operator decision 2026-09-08).
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
   id. Spawn mechanics: E8. Workers are **ephemeral**: one ticket, torn down at signoff; only
   the SA session may persist (operator decision 2026-09-08).
2. **Bootstrap.** Default daemon-create route injects the superpowers bootstrap via SessionStart
   (VERIFIED — T-005 direct JSONL receipt); role-cast is then the first skill (Spec A §Seat
   loading Path 2, satisfied). Untested routes — takeover-spawned, non-default settings, Codex
   daemon spawns (parked until a Codex worker is actually routed) — use the inline fallback:
   the spawn brief itself carries the
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
| 1 | A live session with no live assignment behind it | Structural: **workers are ephemeral** — spawned by foreman dispatch for one ticket, torn down at signoff; only the SA session may persist (operator decision 2026-09-08). So a live worker session implies a live assignment. Sweep at wake/rehydration: account session list vs dispatch receipts (visibility VERIFIED) — flags unknown sessions and workers outliving their signoff. No work-start/heartbeat contract: with single-ticket ephemeral workers, session existence IS the work-start event (operator decision — the drift-inside-a-known-session scenario was manufactured) | Unknown session → escalate to operator (cannot attribute). Worker outliving signoff → teardown (lifecycle step 6) | Escalation receipt on the affected ticket; unattributable → STATUS note | Session list unavailable → fail closed: no dispatch, no acknowledgment; escalation per §Escalation availability | Foreman (process); operator if plan implicated |
| 2 | Change to scope / design / a ticket's definition of done (incl. unauthorized status transitions) | Raised: worker message to foreman. Silent: caught at **signoff** — every worker's output gets an independent review (lifecycle step 5), and the checker brief always asks: did anything outside the ticket's files change; were spec files or ticket `## Scope` / `## Acceptance criteria` / `Status:` headers touched without a matching raise receipt? Workers are ephemeral and one-at-a-time, so the exposure window is one ticket's flight and signoff always runs. No standing watcher (operator decision 2026-09-08 — it duplicated the signoff review) | Raised → route to decision-class owner (plan/scope → operator; content → sa). Unraised change found at signoff → process breach: block acknowledgment, escalate | Receipts on the affected ticket | Signoff review is mandatory before `done` (Spec C lifecycle) — no review, no acknowledgment: fail closed | Operator (plan); sa (content); foreman (breach) |
| 3 | Irreversible / outside-world action | **Source = the raised event only** (worker's raise message; raising is a Spec A seat duty). **Unraised instances are unobservable pre-action in v1** — layer 1 is off; this is the concept's stated, operator-accepted consequence ("the one thing v1 cannot do is pre-empt"). Compensations reduce exposure but do not observe: layer-2 scoping (tool not granted ≠ tool misused), Hermes post-hoc verification (VERIFIED mounts + code graph) | Raised → escalate to operator; **gate progression freezes** (no acknowledgment, no transition, no further dispatch). No mechanical worker stop is claimed; stop candidate = session stop (E8), not relied on | Escalation receipt on ticket | Escalation transport degraded → gates stay frozen; §Escalation availability applies | Operator |
| 4 | Rabbit-hole (one ticket consuming far more than expected) | Foreman judgment at check-in: polling a worker (`session_status_get` / `session_wait_idle` — catalogue VERIFIED; live semantics E8), the foreman judges whether time-on-ticket is out of proportion. No formal per-ticket budget or breach bookkeeping (operator decision 2026-09-08 — formalize only if dogfooding shows judgment isn't enough) | Smells long → commission a scope checker (judgment, never script); verdict routes per acceptance owners | Checker commission + verdict receipts on the ticket | Polling unavailable → caught at next wake sweep; gates stay closed meanwhile | Foreman (raise); operator (if plan change) |

## Escalation transport

- **Primary: operator's phone via Happy.** The foreman session is visible and chattable on the
  phone (dual attach VERIFIED live). Whether foreman activity generates a *push* (vs. requiring
  the operator to look) plus delivery latency = **E4** — load-bearing for the V1 "phone push"
  decision.
- **Fallback: Hermes → Telegram — operator/Hermes-initiated.** The notify path is live in
  operator infra (DECLARED), but the foreman has no channel to trigger Hermes (Hermes hosts no
  daemon); building one is deferred — revisit at M1 only if the manual loop proves
  insufficient (operator decision 2026-09-08). What IS available when the driving
  surface is down: the foreman writes the escalation into its own session and the affected
  ticket — both visible (phone/web; git) — and the operator (or the operator asking Hermes)
  closes the loop.
  Automatic fallback dispatch does not exist in v1.
- **Blocking semantics (v1) — gate-blocking, not execution-stopping.** An open escalation
  freezes gate progression: the affected ticket cannot transition, no acknowledgment is issued,
  no further dispatch happens, and the worker gets no answer. It does **not** mechanically stop
  a running bypass worker — v1 has no pre-action stop (matrix #3); holding after a raise is the
  worker's seat duty (Spec A), and the machinery's guarantee is the gate alone. Escalations are
  receipts on the ticket; open ones survive foreman death there.

## Failure and recovery (invariants concretized)

- **Durable state** = tickets, backlog, receipts, STATUS, specs, git history. The
  foreman session holds nothing authoritative (concept invariant).
- **Fail closed — scoped to what is actually enforced:** every gated act (dispatch,
  acknowledgment, status transition, answering a raise) is a foreman act. Dead foreman, broken
  MCP, lost connection → those acts cannot happen → **ticket/gate progression stops, by
  construction; there is no degraded ungated mode for gates.** A live bypass worker mid-task is
  NOT stopped by foreman death — it runs its current work to completion and then waits
  unanswered (same v1 limit as matrix #3).
- **Foreman death → respawn + rehydration** (normative procedure, lives in `foreman/FOREMAN.md`):
  read PROCESS.md roles binding → backlog → all non-`done` tickets → STATUS; list live
  sessions; reconcile (matrix #1 sweep). Orphan worker found: **stop it and redispatch fresh
  from the ticket** under the fresh-spawn preconditions (§Handoff); adopting a live orphan via
  takeover only after E1. Detection in v1 is operator/Hermes-initiated (manual); an automated
  foreman-death watchdog is deferred to M1 observation.
- **Checker failure** = unresolved check: report, retry, or operator-approved degraded proceed —
  receipt required, never silent substitution (concept invariant carried).

### Foreman notes — no new artifact (operator decision 2026-09-08)

The proposed separate foreman journal is **rejected**: it duplicated the existing system. The
foreman's records land in the artifacts Spec C already defines — ticket-scoped events as
receipts on the ticket (dispatch, escalation, disposition, discard, acknowledgment), infra and
handover state in `docs/STATUS.md`. Spec C stays unchanged; this spec defines no format.

## Foreman session config (thinness, mechanically)

- **Write boundary (operator rule, 2026-09-08):** the foreman may write **operational state
  artifacts** — receipts, `Status:` headers, backlog index rows, STATUS.md, escalation
  records — and never **project intent artifacts** — specs, ticket `## Scope` /
  `## Acceptance criteria`, architecture docs. Mechanically: allowlist covers
  tickets/backlog/STATUS paths + git commit of those (depth per E3); the intent sections inside
  tickets are out of bounds to it exactly as they are to the builder (Spec C immutability rule,
  extended to the foreman seat — Spec A prohibition updated same day). No code-edit tools, no
  builder tooling. Enforcement = spawn allowlist + role-cast prohibitions (Spec A) + everything
  it does being receipts (audit).
- **Model — selection rule, not vibes:**
  1. Must be drivable as a Happier session: today that means Claude Code or Codex CLI (the two
     VERIFIED backends).
  2. The foreman never authors domain solutions, so it does not need the strongest reasoner —
     it needs rule-holding: follow PROCESS, refuse to improvise, stay thin. Mid-tier is
     acceptable; discipline beats brilliance in this seat.
  3. Different family from the project's SA so blind spots don't align (concept routing). With
     two supported backends this is concrete: SA on Claude → foreman on Codex, and vice versa.
  4. The concrete model id is bound per-project in PROCESS.md's roles table at spawn time;
     changing it is a plan change (operator).
  V1 default for this project: SA = strongest available Claude; foreman = Codex.

## Hermes integration

- **Spawn trigger:** wiring VERIFIED (authentication, MCP entry, cross-visibility; requester and
  runner recorded separately — audit distinguishes "Hermes asked" from "host ran"); end-to-end
  Hermes-triggered spawn deferred — operator spawns by hand in v1 (operator decision).
- **Evidence:** Hermes verifies claims against project mounts + code graph without trusting the
  claimant (VERIFIED in operator infra). The foreman→Hermes *request channel* is unbuilt and
  deferred (see §Escalation): foreman-commissioned verification runs as fresh checker sessions
  with repo read access (available now); Hermes evidence stays operator-initiated. This respects "Hermes talks to no workers in normal operation"
  (operator-set).

## Repo layout (new harness repo — minimum to express runtime boundaries)

```
<harness-repo>/                 name = operator decision at M0
  README.md                     what it is; depends on sd-superpowers; sd-superpowers stays standalone
  PROCESS.md                    roles table binding seats to holders (Spec C format)
  backlog.md  tickets/          its own project management (Spec C schema)
  docs/STATUS.md                infra/handover state (Spec C doc standard)
  foreman/FOREMAN.md            foreman boot + rehydration procedure (normative, §Failure)
```

Nothing is listed before it exists (operator decision 2026-09-08): the build phase adds code,
scripts, and adapter notes as they come into being; experiment results are filed under `docs/`
as they run. Any script written must operate on explicit IDs and recorded evidence only
(concept §Script vs model). Everything foreman-runtime currently on branch
`foreman-role-concept` migrates here at M0 (PROCESS header rule). Codex workers: supported via
Happier's Codex backend (VERIFIED in source); their bootstrap path is untested and parked until
a project actually routes a Codex worker — inline-brief fallback if needed. Codex layer-1
machinery (app-server approvals) stays parked with path B.

## Experiments (all named, none gate M0; trimmed to load-bearing 2026-09-08)

| ID | Question | Gates |
|---|---|---|
| E1 | Takeover of a realistic session: long transcript, tool results, MCP-derived fact; early/middle/recent recall; cwd; post-takeover tool+MCP work (T-004 requirement) | Handoff primary path; orphan adoption |
| E2 | `IS_SANDBOX=1` inheritance on all spawn routes (incl. takeover) and across daemon restart | Worker spawn reliability (M1) |
| E3 | Happier spawn-scoping surface: which of tools/permission-mode/model/workdir are settable per spawn; preauthorization semantics under bypass (explicit Write preauth + non-preauth'd gated op — T-004 falsification) | Layer-2 depth; foreman thinness enforcement |
| E4 | Does an escalation actually reach the operator's phone (push vs having-to-look), and how fast | Escalation transport (M1) |
| E8 | Driving-surface shakedown: live semantics of `session_spawn_new`, `session_message_send`, `session_status_get`/`session_wait_idle`, session stop/teardown, exercised end-to-end on one worker | Every runtime mechanism; M1 entry |

Cut (operator decision 2026-09-08, manufactured or premature): rabbit-hole threshold
calibration (settings, not an experiment); foreman→Hermes channel (v1 use died with the manual
fallback); Codex daemon-spawn bootstrap (parked until a Codex worker is routed);
Hermes-triggered foreman spawn (operator spawns by hand in v1). E-numbering kept stable.

## Operator decisions (2026-09-08, recorded)

1. **No separate foreman journal** — notes go into the existing artifacts (ticket receipts;
   STATUS). Spec C unchanged. Applied: §Foreman notes.
2. **Workers are ephemeral** — builder/checker sessions exist only for their scoped work and
   its signoff; only the SA session may persist. The heartbeat/drift-in-a-known-session
   scenario was manufactured and is dropped. Applied: matrix #1, lifecycle.
3. **T-003 Scope wording amended** (operator-approved) — "worker blocks" → gate-progression
   freeze; no mechanical stop claimed in v1. Applied: ticket Scope, matrix #3, §Escalation.
4. **Simplification pass (all four trims approved):** standing rules-tampering watcher removed
   — signoff review asks the question instead (matrix #2); experiments trimmed to load-bearing
   (§Experiments); rabbit-hole effort envelope replaced by foreman judgment at check-in
   (matrix #4); harness repo layout cut to the five files the process needs (§Repo layout).

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
- Every mechanism cites a verified receipt or names an experiment: evidence tags + E1–E4/E8
  throughout (unexercised MCP actions → E8; deferred mechanisms carry no claims). ✔
- V1 boundaries honored: §V1 boundaries. ✔
- Fail-closed + recovery invariants concretized — and scoped to what the machinery actually
  enforces (gates, not worker execution): §Failure and recovery. ✔ (ticket Scope wording
  amended with operator approval — §Operator decisions).
- Codex review round 1: 10/10 applied. Targeted re-review: 8 resolved; finding 5 completed in
  this draft (working-tree sweep + per-file match rule); finding 8 restructured as a submitted
  Spec C amendment; N1 → open item 3; N2 applied. Reviews: `docs/superpowers/foreman/reviews/`.
  Operator approval: pending.
