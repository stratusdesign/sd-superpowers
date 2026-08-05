# Behavioural evaluations — actor coverage + Codex co-design skill edits

Covers the 2026-08-05 edits to `skills/brainstorming/SKILL.md` and `skills/writing-plans/SKILL.md`.
Baseline = the skills before the actors-table/co-design edits (upstream `f071540`). Baseline
statements below are instruction-grounded (what the baseline text did or did not require), not
claims about observed baseline runs, except where a transcript is cited.

Each scenario is designed as a live probe: run the skill on the scenario prompt and judge the
transcript against Expected.

| # | Scenario | Baseline (instruction-grounded) | Expected post-change behaviour |
|---|---|---|---|
| A | Trivial config correction (one actor, one CLI surface, one exact value) | No instruction required any actor analysis | Compact actor statement only; no full table; co-design skipped; design gate stays proportionate |
| B | Browser user + shell agent workflow | No instruction required per-surface analysis; a single-pipeline design could pass every gate (observed: the 2026-08 corpus-upload build) | Separate rows per surface; capability constraints identified; design never assumes the browser user can move files or run commands |
| C | Human + scheduled automation | No instruction required identifying the automation's runtime or state | Automation appears as its own row with runtime and materially applicable capability constraints (state, credentials where relevant) and an observable success test; failure handling addressed in the design |
| D | Evidence hints at another consumer but doesn't prove it | No instruction required marking uncertainty | Marked `UNCONFIRMED` (which triggers the full table); no invented requirements; clarified before approaches if material |
| E | Consequential API/agent-workflow decision, multiple plausible approaches | No co-design step existed | Co-design invoked after approaches exist; returns concrete positions + material disagreements; primary agent synthesizes, does not vote |
| F | Small routine feature, no open design decision | n/a (no co-design step existed) | Co-design skipped although available; later review gates still run as appropriate |
| G | Codex proposes broader architecture without evidence or actor need | n/a | Expansion rejected as preference-only; no repeat loop triggered |
| H | Codex unavailable | n/a | Absence disclosed; workflow continues normally; no invented result |
| I | Codex invocation fails mid-flight | n/a | Failure reported; retry or explicit degraded continuation offered; no silent substitution |
| J | writing-plans receives a spec with no actor analysis | No instruction addressed a missing actor analysis; the plan proceeded on the spec as-is | Material actors reconstructed from the spec; uncertainty marked; only necessary clarification asked; no task-per-row; traceability section added when reconstruction yields multiple actors; does not proceed with obviously incomplete actor scope |

## Run status per scenario

| # | Baseline run | Post-change run |
|---|---|---|
| A | none (instruction-grounded only) | NOT RUN |
| B | 2026-08 corpus-upload build transcript (the motivating failure — not a run of this scenario prompt) | NOT RUN |
| C | none | NOT RUN |
| D | none | NOT RUN |
| E | none | workbench co-design transcript (2026-08-05) exhibits the expected behaviour, but was not a run of this scenario prompt |
| F | n/a | NOT RUN |
| G | none | workbench co-design transcript: preference-only expansion rejected without a loop — not a run of this scenario prompt |
| H | n/a | NOT RUN (rule text verified byte-consistent with the Design Review Gate's absence rule) |
| I | n/a | NOT RUN (rule text verified byte-consistent with the gate's invocation-failure rule) |
| J | none | NOT RUN |

No scenario has a post-change transcript from its own scenario prompt yet.

## Verified so far (instruction-level)

- The governing rule text for each Expected column exists in the current skill files and does not
  contradict the checklist, the process-flow diagram, or the Design Review Gate (checked by
  reading the full files after each edit).
- H/I degradation rules are consistent with the Design Review Gate's capability-absence vs
  invocation-failure split.

## Owed

Live probes for all ten scenarios (fresh sessions, scripted-user harness for the dialogue-driven
brainstorming flow, judged from transcripts). Run when the skills next change materially or on
request.
