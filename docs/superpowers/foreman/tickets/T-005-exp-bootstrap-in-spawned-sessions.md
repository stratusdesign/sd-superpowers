# T-005 — Experiment: superpowers bootstrap in Happier-daemon-spawned sessions

Status: review · Deliverable: #1,#3 · Owner: sa

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

## Result

RAN 2026-09-08. **Positive on the tested path.** A Happier-daemon-spawned worker (default
spawn: `happier session create`, no custom settings/env beyond the standing daemon
`IS_SANDBOX=1`; model resolved to claude-fable-5-1) answered from context, tools forbidden:
confirmed the bootstrap present ("1) YES") and quoted its core rule verbatim — "Invoke relevant
or requested skills BEFORE any response or action — including clarifying questions, exploring
the codebase, or checking files" — plus the 1%-threshold rule. That text exists only in the
injected using-superpowers bootstrap, so SessionStart injection fires on the Happier/Agent-SDK
spawn path.

Scope of evidence: one worker, one spawn route (daemon default create), one machine. Not tested:
takeover-spawned sessions, non-default settings sources, other machines. Spec A's Path-2
conditionality is SATISFIED for the primary route; the inline-brief fallback remains specced but
should not be needed.

Consequence for T-003: workers get skills (and the future role-cast) for free via the bootstrap;
the harness only needs to deliver the spawn-brief line (Spec C format).
