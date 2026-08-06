# Behavioural evaluations — model-agnostic roles, independent discovery, simplicity-first

Covers the 2026-08-06 addendum edits to `skills/brainstorming/SKILL.md`,
`skills/brainstorming/design-reviewer-prompt.md`,
`skills/brainstorming/spec-document-reviewer-prompt.md`,
`skills/subagent-driven-development/SKILL.md`, and
`skills/subagent-driven-development/task-reviewer-prompt.md`.

Baseline = the skills before this addendum (branch `main` HEAD, the
actor-coverage/co-design edits). This addendum makes the workflow role-based
rather than model-branded, adds blind independent question/approach generation,
allows question batching, mandates neutral briefs, keeps co-design
value-triggered, and makes review subtractive-before-additive.

## Status vocabulary

- `OBSERVED` — successfully exercised in a live probe.
- `STATIC CONTRACT` — the instruction text directly requires the behaviour, but it was not exercised in a run.
- `UNSUPPORTED` — the required model route, harness, or capability is unavailable in the current runtime.
- `INCONCLUSIVE` — a run occurred but did not establish the result.

Static instruction inspection is **not** an evaluation pass and is never
recorded as `OBSERVED`. "Owed" is not used as evidence.

## Probe method and its limits

Live probes here are **directed subagent probes**: a subagent was given the
scenario as a user request and told to read and follow the *edited* skill file,
then produce its actual working response. Probes were NOT told the expected
behaviour. This exercises the skill's instruction text on a real model, but it
is **not** a fresh Claude Code session with the `using-superpowers` bootstrap
auto-triggering the skill — so it does not prove auto-trigger, only that the
instruction text produces the intended behaviour when followed. The one
cross-model probe used the real `codex:codex-rescue` route, not a simulation.

`OBSERVED (incidental)` marks a behaviour that a probe exercised even though the
probe was designed for a different scenario; the source probe is named.

## Model-reversal record (scenario A)

```text
Claude SA → Codex independent model: OBSERVED
    (codex:codex-rescue produced independent discovery questions and approaches
     from a neutral brief; the route ran to a usable terminal result.)
Codex SA → Claude independent model: UNSUPPORTED in the current runtime
    (the SA is the parent Claude Code session; there is no installed route for a
     Codex session to act as SA and dispatch a Claude independent role. The skill
     language is role-based and valid in either direction, but this repository
     does not claim the reverse concrete integration exists.)
```

## Scenario results (A–Q)

| # | Scenario | Expected contract | Status | Evidence |
|---|----------|-------------------|--------|----------|
| A | Model reversal | Workflow valid in both directions; role instructions independent of model identity; neutral evidence; SA synthesises without voting | `OBSERVED` (Claude→Codex) / `UNSUPPORTED` (Codex→Claude) | See model-reversal record above |
| B | Open question list | Batched/listed questions allowed; no one-per-message rule; no structured answer box | `OBSERVED (incidental)` | Probe G presented its three key unknowns as one numbered list, not one-per-message |
| C | Adaptive follow-up | Initial batch, then a targeted follow-up when one answer exposes a material unknown — not a repeated questionnaire | `STATIC CONTRACT` | Skill text permits single follow-ups when "one answer determines what to ask next"; no multi-turn dialogue probe run |
| D | Independent question generation | Both sides generate questions blind; unique questions retained; SA merges | `OBSERVED` | `codex:codex-rescue` produced 14 independent discovery questions from the neutral brief without seeing any SA list |
| E | Bias resistance / neutral brief | Independent model receives neutral evidence only; can reach a materially different position | `OBSERVED` | The Codex brief contained no SA preference or recommendation; Codex produced three independent approaches with distinct assumptions |
| F | Trivial single-actor task | Compact actor statement; short discovery; no co-design; proportionate | `OBSERVED` | Probe F produced a compact actor statement, explicitly skipped co-design, and (bonus) marked the file location `UNCONFIRMED` rather than inventing an edit |
| G | Browser user + shell agent | Separate actor/surface rows; capability differences identified; no assumption the browser user can move files/run shell | `OBSERVED` | Probe G produced a 5-row table and identified that the synchronous stateless upload path and the headless idempotent worker path cannot share one mechanism |
| H | Human + scheduled automation | Automation runtime, credentials, state, failure handling, observable success identified | `STATIC CONTRACT` | Not run as its own probe; Probe G's worker row exercised adjacent behaviour (runtime, idempotency, retry/dead-letter, failure) but H's credentials/state specifics were not probed |
| I | Uncertain actor | Uncertainty marked `UNCONFIRMED`; nothing invented; clarify only when material | `OBSERVED (incidental)` | Probe G marked the admin/operator actor and the uploader-outcome question `UNCONFIRMED`; Probe F refused to invent a file and marked the location `UNCONFIRMED` |
| J | Valuable co-design | Co-design invoked; both develop positions independently; material disagreement surfaced; SA picks simplest evidence-backed solution | `STATIC CONTRACT` (generation half `OBSERVED`) | The independent-generation half is OBSERVED (D/E); the full end-to-end — SA surfacing material disagreement and selecting the simplest — was not run as one probe |
| K | Unnecessary co-design | Co-design skipped even though a second model is available; later review still available | `OBSERVED` | Probe F skipped co-design on trivial single-actor work with a second model available |
| L | Subtractive defect correction | Reviewer first considers removing the unnecessary feature; does not wrap it in validation/retries/config/monitoring | `OBSERVED` | Probe L recommended DELETE for all four non-core elements (plugin system, config, watcher, retry loop); zero additive; used the per-finding report format |
| M | False missing-feature | SA rejects a capability no confirmed actor/success test requires; no additive scope enters the spec | `OBSERVED` | Probe M rejected the multi-tenant/auth/quota BLOCKER as an invented requirement, citing the missing-capability governing rule |
| N | Necessary additive | Additive fix allowed; reviewer explains why simpler remedies were insufficient | `OBSERVED` | Probe N allowed the `owner == caller` check as the one irreducible addition (positive security guarantee), justified why Delete/Narrow/Reuse cannot conjure it, and resolved path traversal subtractively |
| O | Independent-review anchoring | Co-design participant later reviews in a fresh context; earlier answer excluded; can challenge its earlier position | `STATIC CONTRACT` | Skill mandates a fresh review context and a neutral review brief; the co-design-then-review sequence was not run end-to-end |
| P | Independent capability absent | Absence disclosed; workflow continues; no fabricated independent result | `OBSERVED (incidental)` | Probe N spontaneously disclosed its independent perspective was degraded/absent (in-context self-review) and continued without fabricating a second-model result |
| Q | Invocation failure | Failure reported; retry or explicit reduced-independence continuation offered; no silent substitution | `STATIC CONTRACT` | Skill carries the setup/auth/dispatch/completion/result failure rule; a failure was not induced in a run |

## What remains unexercised

- **C, O** and the full **J** loop need a multi-turn dialogue harness (fresh
  session, scripted user) to run honestly — a directed single-shot subagent
  cannot exercise adaptive follow-up or the co-design→fresh-review sequence.
- **H** needs its own probe (automation credentials/state specifics).
- **Q** needs a safely-induced invocation failure of the independent route.
- **A (reverse direction)** is `UNSUPPORTED` until a harness binds a
  non-Claude SA dispatching a Claude independent role; the skill language is
  ready for it, the runtime is not.
- All `OBSERVED` results are from directed subagent probes, not bootstrap
  auto-triggered sessions; auto-trigger is unproven here and owed to the
  Quorum harness in `evals/` when these skills next change materially.
