# Foreman — Concept (v2.2, adapters verified)

> **Status:** Fleshed-out concept, ready for submission to design. Incorporates: the operator
> interview (2026-09-07), two Codex adversarial reviews (v1 and v2 — all 12 v2 findings applied),
> memory-stack receipts (Hermes, PulseMark), and five research passes (Claude Code control
> surface, Happy/Happier, per-harness bootstrap, Codex enforcement surface, interception
> broker). Claims are tagged VERIFIED (receipt in hand),
> DECLARED (operator's operational experience, stated as ground truth by the project owner), or
> EXPERIMENT (needs an empirical test before design freeze).

## Problem (DECLARED)

The operator runs multi-session AI development and is trapped in the middle of it:

- ~90% of what gets escalated to him shouldn't reach him at all.
- Real scope drift — out-of-scope decisions, rabbit holes — reaches him only because he reads
  reasoning logs and halts sessions by hand.
- The drift usually originates with the most capable model in the system (the SA/architect):
  powerful models solve problems but rabbit-hole, and from inside their own reasoning the detour
  looks like progress.
- When the SA or builder commissions its own review, it frames the review toward what it expects,
  so its blind spots stay blind.

Declared from direct operational experience across projects, not measured from transcripts; the
design does not depend on the exact percentages.

## Charter

This fork's V1 brief deliberately avoided building an orchestration layer. The foreman
**supersedes that constraint by design** — it is the next phase of the fork, chosen by the
project owner. The V1 workflow (skills, gates, reviews) remains the process the foreman
supervises; the foreman is the layer that makes it run without the operator in the middle.

## The core idea

The fork already believes in independent checking (co-designer, adversarial reviewer, verifier).
Today the checked party runs its own checks — the SA decides whether they happen and how they are
framed. The foreman moves that discipline up: a party that produces work never judges its own
scope, never frames its own review, never marks its own gate passed. The foreman owns those
functions and hands each judgment to an independent model. The thinking stays with the producers.

**Neutral briefs.** Moving commissioning to the foreman removes producer-framed review; it does
not automatically make briefs neutral. Checker briefs derive from artifacts and standing role
rubrics only — never from producer or foreman advocacy about what to find or not find. (This is
the fork's existing neutral-brief rule, inherited unchanged.)

## Trust stance (operator-set, 2026-09-08)

This is a **process-discipline system, not a security-containment system.** Workers run as root
by explicit decision; a root process that turned adversarial would not need the permission API to
do harm, so "worker abuses the control plane" is not the threat model. The foreman exists to catch
**honest drift** — a well-meaning session leaving scope, rabbit-holing, or skipping process — and
its gates are discipline, audit, and visibility, not containment. Security containment of
adversarial agents is explicitly out of scope for v1. Single shared account and the internal-dev
web ring are accepted for v1 (watch items, not blockers).

**Operator interaction rule:** there are defined times when the operator interacts with a spawned
session directly; outside those, talking to workers is the foreman's job. Prompt-answering is
account-scoped and first-answer-wins — attribution comes from the audit trail, precedence from
this operating rule, not from machinery.

## Topology — a hub, not a chain

```text
You + Strategic Advisor ──► documentation updates
You ──► Foreman
Foreman ──► SA/Architect
Foreman ──► Builder(s)
Foreman ──► Checkers (commissioned per check)
Hermes ──► spawns foremen · independent evidence source beneath everything
```

- The foreman is the **sole normal controller of project worker sessions** (Hermes spawns and
  can recover them; the operator drops in at defined times). The SA writes tickets but never
  dispatches them; the foreman hands work to builders and can slot an independent check between
  spec and build. The spec's author never controls the party implementing it.
- All agent-to-agent influence flows through the foreman or through artifacts. There are no side
  channels between sessions.
- **The artifact rule:** conversations are free; nothing is real until it lands in a doc or
  ticket and the foreman acknowledges it. This applies to the operator too — his steering enters
  as documentation updates (via the Strategic Advisor seat), not as orders down a chain.
  **Direct-session steering authorizes no action until its artifact is committed and the foreman
  has acknowledged it** — the interval between saying a thing and documenting it confers no
  authority, on anyone.

## Roles

| Role | Seat | Does |
|---|---|---|
| **Operator** | Happy (phone/desktop) | Owns the plan. Last stop of escalation. Talks to the foreman directly; may drop into any session — outcomes land as artifacts (see artifact rule). |
| **Strategic Advisor** | operator's own session | Design-phase thinking partner; turns operator intent into documentation updates. (Seat already exists in the PulseMark pattern — VERIFIED, `architect/ARCHITECT.md`.) |
| **Foreman** | per-project Happy session | Holds worker sessions, the process, and the escalation gate. Thin: **judges process and plan alignment, never authors domain solutions.** Routes, checks against the plan, commissions judgment out. |
| **SA / Architect** | worker session | Technical delivery: architecture, specs, tickets, doc maintenance, direct small fixes. Modeled on PulseMark `ARCHITECT.md` (VERIFIED). |
| **Builder(s)** | worker session, one at a time in v1 | Implements tickets; appends Builder Reports incl. deviations; never modifies specs. Modeled on PulseMark `BUILDER.md` (VERIFIED). |
| **Checkers** | fresh sessions, commissioned per check | Judge one question each: scope drift, doc/code sync, review of a spec or diff. Routed by fit and independence, model-agnostic. |
| **Hermes** | existing container | Spawns foremen (incl. via Happier MCP); independent evidence source (project mounts + code graph) to verify any claim without trusting the claimant. Full agent — not restricted to read-only (operator decision 2026-09-08); it simply doesn't talk to workers in normal operation. VERIFIED live in operator infra. |

## Authority — by domain, not a stack

- **Content** (what the answer is): SA and builders decide, within the plan.
- **Process and scope visibility**: the foreman — it can hold a gate, block a gated action,
  refuse to mark a stage complete, and escalate. Its teeth never decide an answer; they force a
  decision to be **raised**. Raising makes a departure visible; **raised is not approved** —
  approval belongs to the owner of that decision class.
- **Acceptance owners by finding class:** plan/scope changes → the operator. Content findings
  from a checker → the producer accepts or rejects them, with the foreman recording the
  disposition. Gate and process breaches → the foreman. Genuinely unresolvable against the
  artifacts → escalate up the pipeline.
- **Plan changes**: the operator. Wanting to change the plan is legitimate and is itself an
  escalation; it is never granted by a producer to itself.

## The reference — by phase

- **Design phase:** no plan exists yet, so the foreman enforces the **process that produces the
  plan** — gates run, reviews independently scoped, artifacts written. The design conversation
  itself stays operator↔SA direct; the foreman is not in that loop (too much back-and-forth).
- **Build phase:** the approved design → architecture → tickets are the reference. The reference
  is **living** — plans change constantly and that is normal. Drift is exclusively an **unraised
  departure**: work or decisions that leave the reference without the change being raised first.

## Enforcement — a contract with per-harness adapters (model-agnostic by requirement)

Enforcement is defined as a harness-independent contract; each harness implements it as deeply
as its capabilities allow, via an adapter. The contract, in strength order:

1. **Pre-action interception** where the harness supports it. Claude Code adapter (VERIFIED
   primitive): a session's **unresolved permission prompts** can be routed to an external tool
   (`--permission-prompt-tool` / SDK `canUseTool`) for approve/deny before execution. Two honest
   narrowings: (a) pre-allowed tools never prompt — interception requires a deliberately
   **fail-closed worker permission policy** that leaves gated actions unresolved (design
   question); (b) the end-to-end loop — callback ➜ foreman ➜ decision ➜ reply — needs a broker, and
   **Happier already is one** (VERIFIED in its source): delegated approval is a protocol
   concept; the MCP action `session_permission_respond` lets an agent session answer another
   session's prompt; pending requests are discoverable (counts, details, and a
   `permission_request` webhook to wake the foreman); and first-answer-wins semantics let the
   operator's phone see every prompt and answer any still-open one (participation, not
   revocation — an answered prompt is final). Runtime confirmation remains
   (EXPERIMENT 8).
   Codex adapter (VERIFIED primitive): the `codex app-server` protocol makes the connected
   client the approver — server➜client `requestApproval` JSON-RPC for commands, file changes,
   permission escalations, and network hosts, with per-turn policy overrides. Happier already
   drives this surface as its default Codex backend. Beneath it, Codex workers get an
   OS-enforced floor Claude lacks: sandbox (bwrap/Seatbelt), execpolicy `forbidden` rules, and
   `requirements.toml` caps. Watch items: app-server is tagged experimental (wire stability),
   and `approvals_reviewer` must stay `user` so Codex's own LLM-approver feature never silently
   replaces the foreman.
2. **Spawn-time scoping** everywhere: workdir, tools/MCP allowlists, permission mode, model —
   the worker never holds capabilities its ticket doesn't need (VERIFIED for Claude; Happier
   adds per-session MCP selection at spawn).
3. **Artifact-level gates** everywhere: ticket status transitions, Builder Reports, stage
   completions — harness-independent by construction, since they live in files.
4. **Post-hoc verification** everywhere: Hermes' independent evidence access (project mounts —
   currently `:ro` as a container fact, not a role constraint — plus code graph) lets any claim
   be verified against reality without trusting the claimant (VERIFIED in operator infra).

A harness with a weak adapter (today: Codex) runs under 2–4 with tighter spawn scoping; the
concept never depends on layer 1 existing for every worker.

## Check moments — and their event sources

The foreman is not a per-step reader. Each check moment needs a defined, observable event source
— only #3 gets one free from layer-1 interception; the others are a named design question:

1. Work starting that isn't tied to an open ticket — needs an event/heartbeat contract
   (candidates: session dispatch is foreman-initiated so work ONLY starts by assignment;
   periodic artifact diffs; worker self-report obligations).
2. A proposed change to scope / design / a ticket's definition of done — arrives as an artifact
   change or a raised request; silent versions are caught by 1's mechanism or post-hoc checks.
3. Irreversible or outside-world actions — arrives via pre-action interception where the adapter
   supports it; via spawn scoping + escalation obligation elsewhere.
4. One ticket consuming far more effort than expected (rabbit-hole smell) — threshold and signal
   are a design question.

## Script vs model

- **Scripts** operate only on explicit identifiers and recorded evidence: a gate's receipt
  exists; a test run's recorded exit status; a ticket ID present on a commit/report. Free, exact.
- **Judgment** — including whether work *semantically* belongs to its ticket, whether tests
  actually verify what the gate intends, whether docs match code — is commissioned to an
  independent checker. Deciding what a command "belongs to" without an explicit ID is a scope
  judgment, not a script.
- The foreman never pays a thinking model for a yes/no and never trusts a script with a judgment.

## Escalation pipeline

1. Producer hits a gate or raises a question.
2. Foreman resolves against the artifacts, or asks the SA.
3. Still unclear → foreman commissions an independent check (e.g. adversarial review) to try to
   resolve it.
4. Still unclear, or a plan change, or irreversible → **operator**: phone push via Happy (or
   Hermes→Telegram); the worker blocks until answered (v1).

Target: the ~90% noise never reaches the operator; the drift he currently catches by reading
logs is caught at the gate, before the action.

## Failure and recovery (concept invariants)

- **Fail closed.** A dead foreman, a broken callback path, or a lost connection stops gated
  work; it never silently degrades to ungated work.
- **State lives in artifacts, not in the foreman's head.** Tickets, reports, gate receipts, and
  STATUS files are the durable state; a foreman session is disposable and replaceable by
  respawn + rehydration from artifacts. (Same property that makes workers restartable.)
- **The operator always has an explicit bypass.** An unanswered escalation blocks the worker,
  not the operator: he can answer, override, or replace the foreman — visibly, as himself.
- Checker failure = an unresolved check, handled like any failed invocation: report, retry, or
  explicitly proceed degraded — never silently substituted.

## Ticketing and documentation (inherited, working pattern)

The PulseMark contract is adopted as the baseline (VERIFIED on disk, previously run in
production by the operator):

- backlog index + ticket files with lifecycle status; specs with acceptance criteria;
- Builder Reports appended to tickets (status, branch, commit, **deviations from spec**, known
  issues);
- hard ownership boundaries (builder never edits specs or architect files);
- fix-vs-spec judgment rules for the SA;
- docs updated to match reality, with the SA owning doc accuracy — and the foreman commissioning
  independent doc/code sync checks, since the SA wrote both.

**Ambiguity handling (operator-set, v1):** PulseMark's behavior is kept deliberately — a builder
hitting a wrong or ambiguous spec **implements its best interpretation and records it as a
deviation in the Builder Report**. The deviation note is the event: foreman catches it → routes
a checker pass over the deviation → SA if needed → SA passes it or writes a corrective ticket.
Non-blocking for the builder, fully visible to the process.

The foreman adds what PulseMark lacked: the tickets become machine-read state — the foreman
reads the same backlog the humans do, and ticket status transitions are the events it supervises.

## Model routing

Model-agnostic throughout — roles are bound to models per project by fit, and **enforcement
never assumes a specific harness** (see the adapter contract above):
- Foreman: a model that holds a line rather than chasing the problem; ideally a different family
  from the SA so blind spots don't align.
- SA: the strongest available reasoner.
- Builders: routed by task weight.
- Checkers: chosen per check for independence and fit; different family where different blind
  spots help. Fresh context always; neutral briefs always.

## Runtime foundation (research receipts)

**Spawning full sessions — already proven in operator infra.** Hermes spawns full Claude/Codex
sessions in tmux with complete config, MCPs, and the superpowers bootstrap wired for both
harnesses (Claude via SessionStart hook in settings.json; Codex via AGENTS.md). VERIFIED
2026-08-12, Una workbench receipt.

**Driving sessions programmatically — VERIFIED two ways (Claude).** CLI multi-turn (`--resume`
with JSON output) and Agent SDK (`ClaudeSDKClient`, concurrent sessions). Spawn-time scoping per
worker is rich: permission mode, allowed tools, MCP config, model, workdir, injected system
prompt. Gap: per-skill enable/disable is not granular (EXPERIMENT / design-around).

**Happy layer — Happier, deployed self-hosted 2026-09-08 (see findings below).**
The ecosystem split: slopus/happy works; happier-dev/happier (independent rewrite, more active)
has the stronger orchestrator surface. Individually VERIFIED from repo code/docs (2026-09-07):
`happier session` CLI (create/send/history/wait/status/stop, per-session MCP selection at
spawn), `happier mcp serve` (drive sessions as MCP tools), resume including **takeover of
existing Claude sessions from their JSONL files** — the design-phase→foreman handoff mechanism —
deep Codex support, dual attach (phone + orchestrator on one session) as core design. NOT yet
verified as one deployable foundation: release-ring gating, concurrent drive + human interject,
permission-prompt surfacing, and takeover-in-practice are the deciding experiments.

**Design-phase handoff, concretely:** operator and SA brainstorm in a normal session; on design
approval the docs are written; the foreman takes over the SA session via resume/takeover — or
spawns a fresh SA that rehydrates from the docs. Both paths exist; choosing is a design decision.

## V1 scope decisions (operator-set)

- One foreman per project; Hermes spawns it (or the operator does, by hand).
- One builder at a time; the SA session may stay live alongside.
- Escalation: phone push, worker blocks until answered.
- Builder ambiguity: PulseMark behavior kept (implement best interpretation + deviation note →
  foreman-routed checker pass).
- Enforcement: model-agnostic contract with per-harness adapters; never Claude-only by design.
- Budget/token-awareness: **out of v1.**
- Cross-project resource arbitration: **out of v1** — parked; natural future seat is Hermes'
  layer, above per-project foremen.

## Experiments required before design freeze

1. Skills bootstrap in headless/SDK-spawned sessions — does SessionStart fire and inject it?
   (Hermes' interactive tmux path is verified; this tests the non-tmux paths.)
2. Happier: concurrent drive + human interject on the same session, mid-turn.
3. Happier: takeover/resume of an arbitrary pre-existing Claude session (the handoff mechanism).
4. Happier: does `session wait` surface pending permission prompts to the driver? (Load-bearing
   for the interception loop.)
5. Happier release-ring gating — which needed features are public-ring vs dev builds.
6. Local spawn endpoint security posture on a shared host (loopback binding).
7. Relay latency/throughput when one foreman drives several sessions.
8. **The interception loop via Happier, end-to-end:** `session_permission_respond` callable
   from the foreman's session-control surface in the shipped release, cross-session on one
   account; wake path (webhook vs polling) and latency; whether the foreman can read the full
   pending request (tool name + input), not just counts.
9. **Codex app-server semantics:** does an unanswered `requestApproval` block indefinitely or
   time out; does a mid-turn deny return control cleanly; how `granular` approval policy
   interacts with an external approver; wire stability across releases (it is experimental).

## Findings from live setup (2026-09-08)

Happier stood up self-hosted (happier.stratus3.co, VPN-only) and connected from CLI, web, and
Android — dual attach confirmed live. Two experiments resolved, one blocked:

- **PARTIALLY VERIFIED — dual attach (exp. 2):** simultaneous attachment proven live; mid-turn
  interjection while an orchestrator drives is still untested. Operator's phone + web client + a
  driven session, all on one
  account, simultaneously. Passes.
- **PARTIALLY VERIFIED — respond action exists (part of exp. 8):** invocation not yet exercised;
  `session.permission.respond` is present
  and callable in the live action catalog (`happier session actions list`).
- **BLOCKED — the permission-gate loop (exp. 8), INVESTIGATE FURTHER:** a worker spawned through
  the Happier daemon **as root crashes on launch**. Claude refuses `--dangerously-skip-permissions`
  under root, and Happier's daemon-create path puts workers in that bypass mode even when
  `--permission-mode default` is passed explicitly (reproduced twice, argv confirmed from the
  claude debug log). So no pending prompt is ever produced, and the foreman has nothing to answer.

  **Operator decision (2026-09-08):** workers run **as root for now** — per-user permission
  isolation would be a large project of its own and is out of scope.

  **Precision:** running as root *forbids* bypass mode — the observed result is fail-stop (crash),
  not a fallback to gated mode. A gated root worker is what the foreman gate wants; getting
  Happier to *launch* one is the open investigation. The old Happy daemon already runs Claude as root successfully with
  `--permission-prompt-tool stdio --permission-mode auto`. The investigation is therefore narrow:
  make Happier's daemon spawn workers in that gated mode instead of yolo/bypass (launch profile,
  account default, or a spawn flag that actually overrides). Not a permissions-management project —
  just steering Happier's spawn mode.

### MCP control surface wired (2026-09-08, later)

`happier mcp serve` verified as the foreman's driving surface — 53 tools including
`session_permission_respond`, `session_user_action_answer`, `session_spawn_new`,
`session_message_send`, `session_status_get`, `session_wait_idle`.

- **Claude Code — VERIFIED live:** a session hosting the MCP server called `session_list`
  successfully (one-line mcp-config).
- **Codex — configured** (`[mcp_servers.happier]`, same stdio pattern as its gortex entry);
  not yet exercised.
- **Hermes — wired as a second machine:** happier CLI installed in its container, authenticated
  as its own machine identity on the account, MCP entry registered in its config. Sessions are
  account-scoped, so everything any party spawns is visible on every client (verified: Hermes
  listed the host's sessions from inside its container). Requester and runner are recorded
  separately — the audit trail distinguishes "Hermes asked" from "host ran".
- Operator decision: Hermes is a full agent (not read-only); it holds session-control but does
  not talk to workers in normal operation. Foreman = a Happier session the operator can talk to
  directly; Hermes spawns it (targeting the host machine's daemon, since the container runs none).

## Open design questions (for the design phase)

- The fail-closed worker permission policy: which actions are deliberately left unresolved so
  they route to the foreman. (Claude: the evaluation order means anything pre-allowed never
  reaches the broker; Codex: `approval_policy` + execpolicy verdicts play the same role.)
- Event sources for check moments 1, 2 and 4 (dispatch-only work-start vs heartbeats vs
  artifact diffs; rabbit-hole signal and threshold).
- Handoff mechanics: takeover-existing-session vs fresh-spawn-from-docs (takeover verified in
  code/docs, not yet exercised).
- The foreman's own session config: which tools/MCPs it gets; how its thinness is enforced
  (its own spawn scoping is the obvious lever).
- Which existing fork skills move commissioning up to the foreman vs stay SA-run in
  foreman-less projects (the fork must still work standalone).
- Ticket schema: how much PulseMark structure is adopted verbatim vs adapted (incl. enforced
  ticket IDs on commits/reports so scripts can stay purely mechanical).
