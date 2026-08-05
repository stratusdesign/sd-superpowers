# Behavioural evaluations — actor coverage + Codex co-design skill edits

Covers the 2026-08-05 edits to `skills/brainstorming/SKILL.md` and `skills/writing-plans/SKILL.md`.
Baseline = the skills before the actors-table/co-design edits (upstream `f071540`).
Each scenario is a live probe: run the skill on the scenario prompt and judge the transcript
against Expected. "Verified" below records what has actually been checked so far.

| # | Scenario | Baseline behaviour | Expected post-change behaviour |
|---|---|---|---|
| A | Trivial config correction (one actor, one CLI surface, one exact value) | No actor analysis at all | Compact actor statement only; no full table; co-design skipped; design gate stays proportionate |
| B | Browser user + shell agent workflow | Single-pipeline designs pass review (the observed failure) | Separate rows per surface; capability constraints identified; design never assumes the browser user can move files or run commands |
| C | Human + scheduled automation | Automation runtime/state usually implicit | Automation's runtime, state, credentials, failure handling, and observable success all appear as a row |
| D | Evidence hints at another consumer but doesn't prove it | Silently included or silently dropped | Row marked `UNCONFIRMED`; no invented requirements; clarified before approaches if material |
| E | Consequential API/agent-workflow decision, multiple plausible approaches | No co-design step existed | Co-design invoked after approaches exist; returns concrete positions + material disagreements; primary agent synthesizes, does not vote |
| F | Small routine feature, no open design decision | n/a | Co-design skipped although available; later review gates still run as appropriate |
| G | Codex proposes broader architecture without evidence or actor need | n/a | Expansion rejected as preference-only; no repeat loop triggered |
| H | Codex unavailable | n/a | Absence disclosed; workflow continues normally; no invented result |
| I | Codex invocation fails mid-flight | n/a | Failure reported; retry or explicit degraded continuation offered; no silent substitution |
| J | writing-plans receives a spec with no actor analysis | Plan proceeds on the spec as-is | Material actors reconstructed from the spec; uncertainty marked; only necessary clarification asked; no task-per-row; does not proceed with obviously incomplete actor scope |

## Verified so far

- **Instruction-level (all scenarios):** the governing rule text for each Expected column exists in
  the current skill files and does not contradict the checklist, the process-flow diagram, or the
  Design Review Gate (checked by reading the full files after the edit).
- **B is grounded in a real transcript:** the 2026-08 corpus-upload build is the baseline failure
  this table exists to prevent (browser Claude cannot move files; shell agents can; every review
  polished the single-pipeline design).
- **E and G are grounded in a real transcript:** the workbench co-design run (positions adopted,
  disagreements flagged, preference-only expansion rejected without a loop).
- **H/I:** the degradation rules are byte-consistent with the Design Review Gate's existing
  capability-absence vs invocation-failure split.

## Not yet run

Live headless probes for A, C, D, F, J (fresh sessions, judged from transcripts). The brainstorming
skill is dialogue-driven, so these need a scripted-user harness; run them when the skills next
change materially or on request.
