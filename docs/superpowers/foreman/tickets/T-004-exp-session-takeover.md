# T-004 — Experiment: takeover/resume of an existing Claude session

Status: open · Deliverable: #3 · Owner: SA

## Spec reference
Concept: "Design-phase handoff" + experiments list item 3.

## Scope
Empirically test Happier taking over an existing Claude session from its JSONL (operator↔SA
design conversation → foreman assumes control). Result decides T-003's handoff mechanism:
takeover vs fresh-spawn-from-docs.

## Acceptance criteria
- Ran against a real pre-existing session; outcome + receipts recorded here.
- Recommendation (takeover / fresh-spawn / both) written into T-003 input.
