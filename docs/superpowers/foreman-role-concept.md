# Foreman — Concept (v2, research-grounded)

> **Status:** Fleshed-out concept, ready for submission to design. Supersedes the v1 sketch on
> this branch and incorporates: the operator interview (2026-09-07), the Codex adversarial review
> of v1, memory-stack receipts (Hermes, PulseMark), and three research passes (Claude Code control
> surface, Happy/Happier, per-harness bootstrap). Claims are tagged VERIFIED (receipt in hand),
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

These are declared from direct operational experience across projects, not measured from
transcripts; the design does not depend on the exact percentages.

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

## Topology — a hub, not a chain

```text
You + Strategic Advisor ──► documentation updates
You ──► Foreman
Foreman ──► SA/Architect
Foreman ──► Builder(s)
Foreman ──► Checkers (commissioned per check)
Hermes ──► spawns foremen · independent read-only verifier beneath everything
```

- The foreman is the **only role that holds sessions**. The SA writes tickets but never
  dispatches them; the foreman hands work to builders and can slot an independent check between
  spec and build. The spec's author never controls the party implementing it.
- All agent-to-agent influence flows through the foreman or through artifacts. There are no side
  channels between sessions.
- **The artifact rule:** conversations are free; nothing is real until it lands in a doc or
  ticket and the foreman is informed. This applies to the operator too — his steering enters as
  documentation updates (via the Strategic Advisor seat), not as orders down a chain. That is
  what makes talking directly to any session safe: the artifact is the event, and an artifact
  cannot be unraised.

## Roles

| Role | Seat | Does |
|---|---|---|
| **Operator** | Happy (phone/desktop) | Owns the plan. Last stop of escalation. Talks to the foreman directly; may drop into any session — outcomes land as artifacts. |
| **Strategic Advisor** | operator's own session | Design-phase thinking partner; turns operator intent into documentation updates. (Seat already exists in the PulseMark pattern — VERIFIED, `architect/ARCHITECT.md`.) |
| **Foreman** | per-project Happy session | Holds worker sessions, the process, and the escalation gate. Thin: routes, checks against the plan, commissions judgment out. Never reasons about the domain. |
| **SA / Architect** | worker session | Technical delivery: architecture, specs, tickets, doc maintenance, direct small fixes. Modeled on PulseMark `ARCHITECT.md` (VERIFIED). |
| **Builder(s)** | worker session, one at a time in v1 | Implements tickets; appends Builder Reports; never modifies specs; flags ambiguity, doesn't decide. Modeled on PulseMark `BUILDER.md` (VERIFIED). |
| **Checkers** | fresh sessions, commissioned per check | Judge one question each: scope drift, doc/code sync, review of a spec or diff. Routed by fit and independence, model-agnostic. |
| **Hermes** | existing container | Spawns foremen; independent read-only evidence source (project mounts + read-only code graph) to verify any claim without trusting the claimant. VERIFIED live in operator infra. |

## Authority — by domain, not a stack

- **Content** (what the answer is): SA and builders decide, within the plan.
- **Process and scope visibility**: the foreman — it can hold a gate, block an action, refuse to
  mark a stage complete, and escalate. Its teeth never decide an answer; they force a decision to
  be **raised**. A producer can always get its way — by raising it.
- **Plan changes**: the operator. Wanting to change the plan is legitimate and is itself an
  escalation; it is never granted by a producer to itself.
- Adjudication when a checker and a producer disagree on substance: content belongs to the
  producer; the foreman records the disagreement and escalates only if it is a plan departure, a
  gate breach, or genuinely unresolvable against the artifacts.

## The reference — by phase

- **Design phase:** no plan exists yet, so the foreman enforces the **process that produces the
  plan** — gates run, reviews independently scoped, artifacts written. The design conversation
  itself stays operator↔SA direct; the foreman is not in that loop (too much back-and-forth).
- **Build phase:** the approved design → architecture → tickets are the reference. The reference
  is **living** — plans change constantly and that is normal. Drift is exclusively an **unraised
  departure**: work or decisions that leave the reference without the change being raised first.

## Enforcement mechanics

**Pre-action gate (VERIFIED mechanism).** Claude Code routes a session's permission prompts to an
external tool (`--permission-prompt-tool` / Agent SDK `canUseTool`): every gated action goes out
for approve/deny **before it executes**. Pointed at the foreman, this is real pre-action
interception — the foreman literally holds the permission gate for its workers, replacing
after-the-fact log reading. Receipt: code.claude.com/docs permissions + agent-sdk docs.

**Check moments.** The foreman is not a per-step reader. It checks when:
1. work starts that isn't tied to an open ticket;
2. a change to scope / design / a ticket's definition of done is proposed;
3. anything irreversible or outside-world fires (arrives naturally via the permission gate);
4. one ticket consumes far more effort than expected — the rabbit-hole smell (threshold: a design
   decision).

**Script vs model.** Deterministic checks (gate ran? tests pass? action maps to an open ticket?)
are scripts — free, exact. Judgment checks (did this quietly leave scope? do docs match code? how
should this review be scoped so what can be caught is?) are commissioned to an independent
checker. The foreman never pays a thinking model for a yes/no and never trusts a script with a
judgment.

## Escalation pipeline

1. Producer hits a gate or raises a question.
2. Foreman resolves against the artifacts, or asks the SA.
3. Still unclear → foreman commissions an independent check (e.g. adversarial review) to try to
   resolve it.
4. Still unclear, or a plan change, or irreversible → **operator**: phone push via Happy (or
   Hermes→Telegram); the worker blocks until answered (v1).

Target: the ~90% noise never reaches the operator; the drift he currently catches by reading
logs is caught at the gate, before the action.

## Ticketing and documentation (inherited, working pattern)

The PulseMark contract is adopted as the baseline (VERIFIED on disk, previously run in
production by the operator):

- backlog index + ticket files with lifecycle status; specs with acceptance criteria;
- Builder Reports appended to tickets (status, branch, commit, deviations, known issues);
- hard ownership boundaries (builder never edits specs or architect files; ambiguity is flagged,
  not decided);
- fix-vs-spec judgment rules for the SA;
- docs updated to match reality, with the SA owning doc accuracy — and the foreman commissioning
  independent doc/code sync checks, since the SA wrote both.

The foreman adds what PulseMark lacked: the tickets become machine-read state — the foreman reads
the same backlog the humans do, and ticket status transitions are the events it supervises.

## Model routing

Model-agnostic throughout. Roles are bound to models per project by fit:
- Foreman: a model that holds a line rather than chasing the problem; ideally a different family
  from the SA so blind spots don't align.
- SA: the strongest available reasoner.
- Builders: routed by task weight.
- Checkers: chosen per check for independence and fit; different family where different blind
  spots help. Fresh context always.

## Runtime foundation (research receipts)

**Spawning full sessions — already proven in operator infra.** Hermes spawns full Claude/Codex
sessions in tmux with complete config, MCPs, and the superpowers bootstrap wired for both
harnesses (Claude via SessionStart hook in settings.json; Codex via AGENTS.md). VERIFIED
2026-08-12, Una workbench receipt.

**Driving sessions programmatically — VERIFIED two ways.** CLI multi-turn (`--resume` with JSON
output) and Agent SDK (`ClaudeSDKClient`, concurrent sessions, asyncio-native). Spawn-time
scoping per worker is rich: permission mode, allowed tools, MCP config, model, workdir, injected
system prompt. Gap: per-skill enable/disable is not granular (EXPERIMENT / design-around).

**Happy layer — use `happier`.** The ecosystem split: slopus/happy works, but happier-dev/happier
(independent rewrite, more active) is ahead on every axis the foreman needs — `happier session`
CLI (create/send/history/wait/status/stop with per-session MCP selection at spawn),
**`happier mcp serve`** (an MCP surface purpose-built for an orchestrating agent to drive
sessions as tools), resume properly solved **including takeover of existing Claude sessions from
their JSONL files** — which is precisely the design-phase→foreman handoff mechanism — deep Codex
support, and dual attach (phone + orchestrator on the same session) as core design. VERIFIED from
repo code/docs, 2026-09-07.

**Design-phase handoff, concretely:** operator and SA brainstorm in a normal session; on design
approval the docs are written; the foreman takes over the SA session via resume/takeover — or
spawns a fresh SA that rehydrates from the docs. Both paths exist; choosing is a design decision.

## V1 scope decisions (operator-set)

- One foreman per project; Hermes spawns it (or the operator does, by hand).
- One builder at a time; the SA session may stay live alongside.
- Escalation: phone push, worker blocks until answered.
- Budget/token-awareness: **out of v1.**
- Cross-project resource arbitration: **out of v1** — parked; natural future seat is Hermes'
  layer, above per-project foremen.

## Experiments required before design freeze

1. Skills bootstrap in headless/SDK-spawned sessions — does SessionStart fire and inject it?
   (Hermes' interactive tmux path is verified; this tests the non-tmux paths.)
2. Happier: concurrent drive + human interject on the same session, mid-turn.
3. Happier: takeover/resume of an arbitrary pre-existing Claude session (the handoff mechanism).
4. Happier: does `session wait` surface pending permission prompts to the driver? (Load-bearing
   for the foreman-as-permission-gate loop.)
5. Happier release-ring gating — which needed features are public-ring vs dev builds.
6. Local spawn endpoint security posture on a shared host (loopback binding).
7. Relay latency/throughput when one foreman drives several sessions.

## Open design questions (for the design phase)

- Handoff mechanics: takeover-existing-session vs fresh-spawn-from-docs (both verified available).
- Rabbit-hole threshold: what signal and limit trigger check moment #4.
- The foreman's own session config: which tools/MCPs it gets; how its thinness is enforced
  (allowlist is the obvious lever — its own spawn scoping).
- Which existing fork skills move commissioning up to the foreman vs stay SA-run in
  foreman-less projects (the fork must still work standalone).
- Ticket schema: how much PulseMark structure is adopted verbatim vs adapted.
