# T-005 — Experiment: superpowers bootstrap in Happier-daemon-spawned sessions

Status: done · Deliverable: #1,#3 · Owner: sa

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
confirmed the bootstrap present ("1) YES") and quoted its core rule and the 1%-threshold rule verbatim.
**Direct receipt (independent review, Codex): the session JSONL records a successful
`SessionStart` hook injecting the full bootstrap as `hook_additional_context`, and the worker
made 0 tool calls.** Injection is directly proven on this path — the quotations are
corroboration, not the primary evidence.

Scope of evidence: one worker, one spawn route (daemon default create), one machine. Not tested:
takeover-spawned sessions, non-default settings sources, other machines. Spec A's Path-2
conditionality is SATISFIED for the primary route; the inline-brief fallback remains specced but
should not be needed.

Consequence for T-003: on the tested default daemon-create route, workers receive the bootstrap
automatically; once role-cast is installed and listed, the harness need only send the
spawn-brief line (Spec C format). Untested routes retain the inline fallback pending
verification.

## Receipts
- 2026-09-08T13:37:28Z · done-claim-review · checker · resolved-credible-with-text-fixes-applied · task-ae50907c74ed104d0
- 2026-09-08T13:37:28Z · acknowledgment · operator · standing-proceed-directive · conversation-2026-09-08
