# T-005 — Experiment: superpowers bootstrap in Happier-daemon-spawned sessions

Status: open · Deliverable: #1,#3 · Owner: sa

## Spec reference
Concept experiments list item 1; T-001 (boot-readable role canon) and T-003 (worker spawn design)
both depend on the answer.

## Scope
Does the superpowers SessionStart bootstrap (and therefore skill auto-triggering + the future role
canon) load in sessions the Happier daemon spawns? Verified for Hermes' tmux path; unknown for the
Happier/Agent-SDK path (settingSources are passed — receipt needed, not inference).

## Acceptance criteria
- A daemon-spawned worker demonstrably has the bootstrap: it must produce evidence of skill
  awareness (e.g. names its using-superpowers obligations when asked), not merely run.
- Result recorded with the spawn config used (settings sources, env); negative result includes
  the injection mechanism T-003 must design instead.
