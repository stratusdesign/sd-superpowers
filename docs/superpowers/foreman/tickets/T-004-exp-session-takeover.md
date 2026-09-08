# T-004 — Experiment: takeover/resume of an existing Claude session

Status: open · Deliverable: #3 · Owner: SA

## Spec reference
Concept: "Design-phase handoff" + experiments list item 3.

## Scope
Empirically test Happier taking over an existing Claude session from its JSONL (operator↔SA
design conversation → foreman assumes control). Result decides T-003's handoff mechanism:
takeover vs fresh-spawn-from-docs.

## Acceptance criteria
- Target session is a genuine non-Happier-born Claude session with real prior conversation;
  provenance (session id, origin, turn count) recorded.
- After takeover: prior conversational state demonstrably intact (worker correctly answers a
  question about earlier turns), AND a foreman-issued follow-up instruction completes.
- Failure modes and fallback behavior recorded, not just the happy path.
- Recommendation written into T-003 input — "both" is only valid as fresh-spawn-as-fallback,
  never as two co-equal production paths.
