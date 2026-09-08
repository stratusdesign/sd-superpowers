# T-004 — Experiment: takeover/resume of an existing Claude session

Status: done · Deliverable: #3 · Owner: sa

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

## Result — RAN 2026-09-08, independently reviewed (Codex) 2026-09-08

**Native-session import/resume demonstrated; two seeded facts survived.** ("Takeover" here means
import-and-respawn per Happier's mechanism — process continuity is not claimed. Recalling two
recently-seeded tokens proves limited conversational recall, not intact context generally.)
- Provenance: native `claude -p` session `2836faab-66e5-4e16-9bb4-d98f545d5f18`, 2 turns, seeded
  with codeword `MARIGOLD-47` and animal `HERON`. Not Happier-born.
- Takeover via web Direct → "Resume Claude session" (imports the JSONL, respawns as a persisted
  Happier session). After takeover the worker answered from prior state: **"MARIGOLD-47. HERON."**
  → conversational context survived the takeover.
- Follow-up action (write `takeover-proof.txt`): `Write` was **available but not preauthorized**
  — the JSONL records a permission request with an immediate error result, then a normal
  end-turn. No request remained pending when checked; the trace does not establish whether it was
  briefly represented as pending. Establishes "this worker's write required permission," not a
  general gating claim. Feeds T-003: the foreman must distinguish immediate refusal from a
  request that becomes pending and needs routing/timeout handling.

**Failure modes seen:**
- First attempt crashed on the root/bypass blocker — the web spawn predated the path-A fix.
- Fix applied and verified: `IS_SANDBOX=1` injected into the Happier **daemon** env via systemd
  drop-in (`~/.config/systemd/user/happier-daemon.default.service.d/10-is-sandbox.conf`);
  daemon children should inherit it — confirmed on one fresh default worker; other spawn routes
  (incl. takeover) and post-restart behavior to be confirmed at T-003. Path-A operational config
  for v1.
- Note: this worker's write required permission even with IS_SANDBOX set — suggestive that
  bypass-enablement ≠ auto-allow-everything, but one refused Write on one worker is not a general
  claim. Cheap falsification for T-003: spawn with an explicit Write preauthorization and a
  non-Write gated operation; observe both.

**Recommendation into T-003:** takeover is the **leading candidate** for the design→foreman
handoff — validate before making it primary (evidence covers one 2-turn text-only session; not
long/compacted transcripts, tool results, MCP state, or changed cwd). Required validation, in
T-003: take over one realistically long session containing tool results and an MCP-derived fact;
verify early/middle/recent recall, cwd identity, and post-takeover tool+MCP work. Fresh-spawn
remains the intended fallback. Spawn scoping should preauthorize the tools a ticket needs.
