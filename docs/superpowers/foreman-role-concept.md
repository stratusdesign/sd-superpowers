# Foreman Role — Concept Sketch

> **Status:** Candidate under discussion. Not approved, not a spec. Open decisions marked ⬒.
> Built collaboratively 2026-09-07. Next step is your judgment, then the design review gate.

## In one line

The foreman is an independent overseer that sits **above** the SA, holds the project's
**frozen scope**, and **commissions** every check the SA is conflicted about judging on its own.
Thin, stubborn, with teeth over process and scope — never over the answer.

## Why it exists

- You are heavily in the middle. ~90% of what reaches you shouldn't; the real scope-drift
  reaches you only because you read the logs and halt it by hand.
- The drift usually comes from the SA itself. Powerful models solve problems but rabbit-hole,
  and from inside their own reasoning the detour looks like progress.
- The party that drifts can't police its own drift. The party being reviewed shouldn't frame
  its own review. Both are conflicts of interest — and today they both land on you.

## The one idea

The fork already believes in independent checking (co-designer, adversarial reviewer, verifier).
Today the **SA runs those checks** — it decides whether they happen and how they're framed.
The foreman promotes that discipline from something the checked party runs to something that
**runs over it.**

> **Principle — conflict-of-interest custody.** Anything the SA can't fairly judge about its own
> work belongs to the foreman. The foreman doesn't judge it either — it commissions an independent
> model to. Thinking stays with the SA.

## Three parties (never two)

| Party | Who | Role | Conflict |
|---|---|---|---|
| **Producer** | SA / builder | Does the work | Can't fairly check its own work |
| **Commissioner** | **Foreman** | Owns *what* is checked, *who* checks it, the result, and the teeth | Independent of producer; deliberately **not** the judge |
| **Checker** | Opposite model (e.g. Codex) | Judges | Independent of both; picked for fit + different-family blind spots |

What the foreman commissions out — and never judges itself:

- Did the work stay in the frozen scope? → opposite model (semantic call) over a script box-match
- Did the required gate actually run? → script
- How should this review be scoped, and who reviews it? → foreman picks; independent reviewer judges
- Do the docs match reality? → opposite model (Codex if the SA is Claude)
- Does this need the human? → the foreman's own core act (see Escalation)

## The frozen box

- The box = approved **design → architecture → tickets**. Once set, frozen.
- The SA is held to the box it wrote. Staying inside it is not optional.
- Wanting to change the box is legitimate — but is **itself an escalation**. It goes up; it is
  not the SA's to grant itself. Drift becomes a controlled event instead of a silent one.

## When the foreman looks — trip-wires

Runs free between these. Wakes on:

1. SA about to start work not tied to an open ticket.
2. SA proposing to change scope / design / a ticket's definition of done.
3. Any irreversible or outside-world action.
4. SA burning past a set effort on one ticket without closing it — the rabbit-hole smell. ⬒ threshold to tune.

Checking every step just rebuilds you-reading-every-log. Checking only at ticket edges catches
drift too late. Trip-wires are the middle.

## Script vs model (don't pay a thinker to tick a box)

- **Script** — deterministic, free, no judgment: gate ran? file committed? tests pass? action maps to an open ticket?
- **Model** — judgment, dispatched to an independent checker: did this quietly leave scope? do the docs mean what the code does? is this review scoped to catch what's catchable?
- The foreman never pays a thinking model for a yes/no, and never trusts a script with a judgment.

## Escalation — getting you out of the middle

- **Handled silently / SA proceeds:** anything the box already answers, and all trivia.
- **Escalated to you:** crossing the wall, a request to change the box, irreversible actions,
  and genuine ambiguity the artifacts can't resolve.
- **Ladder:** SA → foreman → you. The foreman resolves against the frozen box first, or asks the
  SA. You are the last stop, not the first.
- **Target:** the ~90% stops reaching you; the drift you currently catch by hand gets caught
  *before* the action.

```text
        ┌─────────── frozen box (design → architecture → tickets) ───────────┐
        │                                                                    │
   YOU ─┤  escalate: wall crossed · box-change request · irreversible · true ambiguity
        │                                                                    │
        └──────── FOREMAN (thin, stubborn, teeth) ───────────────────────────┘
                    │ trip-wire?          │ commission check
                    ▼                     ▼
                  SA / builder  ──────► opposite-model checker (scope · docs · review)
                  (does the work)        (judges; independent of both)
```

## Teeth ⬒ (proposed; open)

- **Teeth on process and scope:** foreman can halt, refuse to mark a stage complete, and escalate.
- **No teeth on the answer:** cannot overrule a decision you or the SA made; cannot invent scope.
- Open: is "refuse to advance a gate" a hard block or a loud flag?

## Model routing

- Route each check for **independence and fit, not cost.** Opposite family where different blind
  spots help.
- The foreman itself wants the **stubborn, least-seducible** model — likely a different family
  from the SA, precisely so it doesn't share the SA's blind spots.

## The thinness guarantee — the line to defend

The foreman routes and holds a box; it does **not** reason about the domain. That thinness is
exactly what removes the need to "check the checker." The moment the foreman starts having domain
opinions, it becomes a second SA with its own blind spots and the guarantee breaks.

## Biggest open decision ⬒ — "on top" means a new harness

- On-top can't live inside Claude Code: the Claude Code session **is** the top tool-loop; nothing
  sits above it there.
- A real on-top foreman is a **separate orchestrator** (Agent SDK or similar) that spawns
  Claude Code / Codex / other sessions as workers and sits over them.
- That makes this a **harness around the fork, not just new skills in it** — a materially bigger
  build. This decision determines what the project actually is.

## Open decisions

1. **On-top harness vs on-the-side skill** — leaning on-top (your call); cost is a new platform.
2. **Teeth** — hard-block vs loud-flag on gate advance.
3. **Trip-wire tuning** — the rabbit-hole effort threshold.
4. **Confirmed** — foreman stays thin; all judgment dispatched to independent checkers.
