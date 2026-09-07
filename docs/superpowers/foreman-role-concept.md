# Foreman Role — Concept Sketch

> **Status:** Candidate under discussion. Not approved, not a spec. Open decisions marked ⬒.
> Built collaboratively 2026-09-07. Next step is your judgment, then the design review gate.

## In one line

The foreman is an independent overseer that sits **above** the SA, checks its work against the
current plan, and runs the checks the SA can't fairly run on itself. Thin, with teeth over process
and scope — not over the answer.

## Why it exists

- You are heavily in the middle. ~90% of what reaches you shouldn't; the real scope-drift reaches
  you only because you read the logs and halt it by hand.
- The drift usually comes from the SA itself. Powerful models solve problems but rabbit-hole, and
  from inside their own reasoning the detour looks like progress.
- The party that drifts can't police its own drift. The party being reviewed shouldn't frame its
  own review. Both land on you today.

## The core idea

The fork already uses independent checks (co-designer, reviewer, verifier). Today the **SA decides**
whether they happen and how they're framed — the checked party running its own check. The foreman
moves that out. The SA can't fairly judge whether it stayed in scope, whether a gate ran, or how its
work should be reviewed — so the foreman owns those and hands each to an independent model. The
thinking stays with the SA.

## Three roles

| Role | Who | Does |
|---|---|---|
| **Producer** | SA / builder | The work. Can't fairly check itself. |
| **Foreman** | independent overseer | Owns *what* is checked, *who* checks it, the result, the teeth. Not the judge. |
| **Checker** | opposite model (e.g. Codex) | Judges. Independent of both; picked for fit + different blind spots. |

What the foreman hands to an independent checker — never judges itself:
- did the work quietly leave scope? (semantic call)
- do the docs match what the code does?
- how should this be reviewed, and by whom?

What's just a script, no model needed:
- did the required gate run? did tests pass? does this action map to an open ticket?

## The plan is the reference

- The foreman measures drift against the **plan / tickets you already have**. No new artifact, no ceremony.
- Plans change — that's normal. The foreman flags only **unraised departures**. Drift = a scope
  change that skipped you, not a plan that moved.

## When the foreman looks

Not every step — that's just you-reading-logs again. It checks at a few moments:

- work starting that isn't tied to an open ticket,
- a proposed change to scope / design / a ticket's definition of done,
- anything irreversible or outside-world,
- one ticket eating far more effort than it should — the rabbit-hole smell. ⬒ threshold to tune.

## Script vs model (don't pay a thinker to tick a box)

- **Script** — deterministic, free, no judgment: gate ran? tests pass? action maps to a ticket?
- **Model** — judgment, handed to an independent checker: left scope? docs honest? review scoped to catch what's catchable?
- The foreman never pays a thinking model for a yes/no, and never trusts a script with a judgment.

## Escalation — getting you out of the middle

- **Handled silently / SA proceeds:** anything the plan already answers, and all trivia.
- **Raised to you:** an unraised departure, a request to change the plan, irreversible actions, and
  genuine ambiguity the artifacts can't resolve.
- **Ladder:** SA → foreman → you. The foreman resolves against the plan first, or asks the SA. You
  are the last stop, not the first.
- **Target:** the ~90% stops reaching you; the drift you currently catch by hand gets caught
  *before* the action.

```text
        ┌─────────── the plan (design → architecture → tickets) ──────────────┐
        │                                                                     │
   YOU ─┤  raised: unraised departure · plan-change request · irreversible · true ambiguity
        │                                                                     │
        └──────── FOREMAN (thin, teeth on process + scope) ───────────────────┘
                    │ check moment?       │ hand off check
                    ▼                     ▼
                  SA / builder  ──────► opposite-model checker (scope · docs · review)
                  (does the work)        (judges; independent of both)
```

## Teeth ⬒ (proposed; open)

- **On process and scope:** foreman can halt, refuse to mark a stage complete, and escalate.
- **Not on the answer:** cannot overrule a decision you or the SA made; cannot invent scope.
- Open: is "refuse to advance a gate" a hard block or a loud flag?

## Model routing

Route each check to an independent model that fits it — chosen for independence and fit, not cost.
The foreman itself wants a model that holds a line rather than chasing the problem, probably a
different family from the SA so it doesn't share its blind spots.

## Runtime shape (resolved)

"On top" can't live inside a single Claude Code session — that session **is** the top tool-loop.
Two facts make it concrete:

- **Full sessions, not subagents.** Subagents run with the skill bootstrap disabled — they don't
  auto-follow process. A full session launched fresh runs its SessionStart hook and auto-fires
  skills. So the foreman spawns *full sessions* as workers, and process-following comes for free.
- **Comms runs down the tree.** Two co-equal sessions can't talk directly, but whoever *launches* a
  session owns its input/output. The foreman is the launcher, so foreman↔worker is a direct channel.

Inside the foreman: deterministic code spawns sessions and does the box-matching; a thin model does
the routing and escalation judgment. Workers are full Claude Code / Codex sessions with fresh,
independent contexts (also the "SA runs lean, restartable" win). Tax: full sessions are heavier than
subagents, and interactive-auth MCP servers (Una, Corpus) are absent unless the harness carries login.

## Happy as spawner + viewport

[Happy](https://github.com/happier-dev/happier) is an end-to-end-encrypted client for Claude Code
and Codex, with a host daemon that spawns sessions and a `happy-agent` CLI.

- **Non-headless on purpose.** Workers run visible and mirror to your phone. Monitoring becomes a
  backstop — you glance when you want, not because nothing else is watching.
- **File-state sidesteps Happy's weak resume.** State lives in files, so the foreman spawns a fresh
  session and rehydrates it rather than reconnecting.
- **The foreman is itself a Happy session** — a direct line to it from anywhere. Workers watched by
  the foreman; the foreman watched by you. That is who checks the foreman: **you**, cheaply, because
  it's the one session you hold a line to. You manage one session, not N.

## Keep the foreman thin

A full session can drift too — the thing the foreman exists to catch. So its judgment stays small:
route, escalate, check against the plan; everything mechanical is a script. Thin enough that watching
it is a glance, not log-reading. If it starts "helping" with the work, it's becoming a second SA.

## Prove this first ⬒

Before betting on Happy as the spawn channel, run one test: **script-spawn a single session via
Happy's daemon and confirm an external orchestrator can BOTH drive it AND see it mirrored on your
phone.** The mirror is solid; an orchestrator's programmatic grip on a Happy-spawned session is the
part their open issues call rough. Works → Happy is spawner and viewport in one. Doesn't → foreman
drives via the Agent SDK and Happy just mirrors alongside. Either way you're not blocked.

## Open decisions

1. **Teeth** ⬒ — hard-block vs loud-flag on gate advance.
2. **Trip-wire tuning** ⬒ — the rabbit-hole effort threshold.
3. **Prove first** ⬒ — the Happy orchestrator-grip test.
4. **Resolved** — on-top = orchestrator spawning full sessions; comms down the tree; foreman is a
   thin Happy session you hold a direct line to; the SA can't judge its own scope/gates/reviews, so
   those go to independent checkers.
