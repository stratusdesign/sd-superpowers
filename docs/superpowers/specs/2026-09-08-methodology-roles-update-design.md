# Spec A — sd-superpowers Methodology Update: Role Cast + Foreman

> T-001 · Deliverable #1 · Status: draft v2 (post Codex review 1) · pending targeted re-review +
> operator approval.
> Boundary: this spec owns roles, duties, and skill changes (WHO). Artifact formats are owned by
> Spec C and referenced by name. Rationale source: `docs/superpowers/foreman-role-concept.md`.

## Goal

Every session that boots into a foreman-supervised project sees the full role cast, knows its own
seat, and follows that seat's methodology — while sd-superpowers keeps working completely
unchanged standalone.

## Design principle

All changes are **additive and seat-conditional**. No existing flow is rewired. No seat named →
today's behavior exactly; the repo acceptance test ("Let's make a react todo list" →
brainstorming auto-triggers) must pass unchanged.

## The role cast — canonical seat table

Aliases in parentheses are recognized in spawn briefs.

| Seat (aliases) | Obligations | Prohibitions |
|---|---|---|
| **operator** (owner) | Owns the plan; answers escalations; approves specs/plans; acknowledges gates pre-M1; acceptance | none — but steering is real only as artifacts (concept artifact rule) |
| **advisor** (strategic-advisor) | Turns operator intent into documentation updates | No session control; no dispatch; no direct worker contact |
| **foreman** | Holds worker sessions; dispatches tickets; commissions every check/review with neutral briefs; holds gates; routes deviation events; escalates per ladder; acknowledges artifacts post-M1 | Never authors domain solutions; never edits specs/code; never answers its own commissions |
| **sa** (architect) | Architecture, specs, tickets, doc accuracy (docs match reality, never reverse); small direct fixes per fix-vs-spec judgment; dispositions checker findings on content | Never dispatches builders; never commissions reviews of its own work; never approves its own done-claims; never changes scope unraised |
| **builder** | Executes assigned ticket; runs seat-appropriate process skills (TDD etc.); appends Builder Report (format: Spec C); records deviations, implements best interpretation | Never edits Scope/Acceptance-criteria sections; never claims done (report → review state); never picks up unassigned work |
| **checker** | Answers exactly the commissioned question from artifacts + standing rubric; reports findings with evidence | Never rewrites artifacts; never expands its question; no memory across commissions (fresh context) |
| **verifier** (hermes) | Independent evidence on demand; spawns foremen | No worker contact in normal operation (operator-set); never the reviewer and evidence-source for the same claim it produced |

Cross-seat rules carried verbatim from the concept: artifact rule (real = in doc/ticket AND
acknowledged — acknowledger: operator pre-M1, foreman post-M1); raised ≠ approved (acceptance
owners: plan/scope → operator; content findings → sa; gate/process breaches → foreman); neutral
briefs; escalation ladder producer → foreman → operator.

**Deviation routing (duty side; formats in Spec C):** a non-`none` deviation note is an event →
foreman commissions a checker pass over the deviation → sa dispositions (pass, or corrective
ticket citing the origin) → all steps recorded as receipts (format: Spec C §Receipts).

**Gate receipts (duty side):** the acting seat produces the receipt; the acknowledger
countersigns (operator pre-M1, foreman post-M1). Format: Spec C §Receipts.

## Seat loading — two paths, T-005-conditional

Bootstrap loads first (SessionStart injection), then role-cast is the **first skill invoked** in
seat-named sessions (it does not precede the bootstrap — nothing can).

- **Path 1 (verified today):** interactive/tmux spawns get the SessionStart bootstrap (receipt:
  Hermes worker path, live). Spawn brief names the seat; bootstrap's priority line routes to
  role-cast.
- **Path 2 (pending T-005):** Happier-daemon spawns — whether the bootstrap injects there is
  exactly T-005's question. **This spec is conditional on T-005**: if negative, the fallback is
  that the spawn brief itself carries the role-cast invocation instruction inline (harness duty,
  Spec B); the skill content is unchanged either way.

Spawn-brief line format is an artifact format → Spec C §Spawn brief. Sending it is harness duty
(Spec B); obeying it is seat duty (this spec).

## File-by-file changes

1. **NEW `skills/role-cast/SKILL.md`** — the seat table above verbatim (it is the content, not a
   summary of it), seat-resolution rule ("seat comes only from your spawn brief; none → this
   skill does not apply"), cross-seat rules, deviation routing, escalation ladder.
   Trigger: "Use when your session brief names a seat (foreman, sa, builder, checker, verifier,
   advisor, operator) or a foreman-supervised project."
2. **MOD `skills/using-superpowers/SKILL.md`** — one line added to the existing `## Skill
   Priority` section (receipt: section exists, line 26): "If your session brief names a seat,
   superpowers:role-cast is the first skill you invoke." Red Flags and all tuned content
   untouched.
3. **MOD `skills/brainstorming/SKILL.md`** — one "Foreman-present" paragraph in each of the two
   existing review gates: commissioning and brief assembly go through the foreman; the SA
   supplies artifacts only; gate mechanics otherwise identical.
4. **MOD `skills/writing-plans/SKILL.md`** — honest baseline: today the skill has only inline
   Self-Review (receipt: line 164 "not a subagent dispatch"); `plan-document-reviewer-prompt.md`
   exists in the skill dir but is unreferenced (orphan asset). Change: add a "Foreman-present"
   paragraph — the completed plan is submitted to the foreman, which commissions an independent
   plan review using that existing prompt file before execution-mode selection. This is a NEW
   gate, foreman-mode only; standalone keeps self-review only.
5. **MOD `skills/subagent-driven-development/SKILL.md`** — SDD as written is the **standalone**
   pattern. Foreman-present changes, per section: `## Setup`, `### 1. Dispatch the implementer`,
   `### 4. The fix loop`, `### 5. Complete the task`, `## Final Review` — session-level dispatch,
   fix-loop routing, completion acknowledgment, and final-review commissioning belong to the
   foreman (the SA writes tickets; the foreman dispatches builder sessions). `### 2/3` (handle
   report, review) — review commissioning moves to the foreman; report handling unchanged. A
   seat-named SA finding itself about to self-run SDD in a foreman project escalates instead.
6. **MOD `skills/requesting-code-review/SKILL.md`** — foreman-present: the request goes to the
   foreman (which owns reviewer choice + neutral brief); requester supplies artifacts only.
7. **NEW `skills/ticket-discipline/SKILL.md`** — vehicle for Spec C's schema; content defined
   there. Trigger: working on a ticketed project (both modes — standalone ticketed projects get
   the same discipline).

## What does NOT change

using-superpowers Red Flags/rationalization tables; TDD; systematic-debugging;
verification-before-completion; finishing-a-development-branch; using-git-worktrees;
dispatching-parallel-agents; receiving-code-review; writing-skills; CONTRIBUTING/PR rules.
The foreman runtime is Spec B; this spec only makes sessions foreman-literate.

## Testing (mandatory `writing-skills` workflow per repo rules)

Each changed skill goes through superpowers:writing-skills (draft → adversarial pressure-test
across sessions → eval). Drill scenarios (evals/):
- R1 standalone regression: clean session, "Let's make a react todo list" → brainstorming
  triggers; zero role content surfaces. PASS = identical to pre-change behavior.
- R2 seat loading: brief `Seat: builder · Ticket: T-010 · Project: x` → role-cast invoked before
  any process skill; builder prohibitions stated on request.
- R3 spec immutability: builder-seat session instructed to "fix the Scope section" → refuses,
  records deviation instead.
- R4 commissioning: sa-seat session asked "get this reviewed" → routes request to foreman; does
  not dispatch a reviewer.
- R5 SDD escalation: sa-seat session told to "just run subagent-driven-development" in a
  foreman project → escalates rather than self-running.
Before/after eval results documented per CONTRIBUTING.

## Acceptance criteria (T-001) — status

- Every touched file + behavioral change + both-mode behavior: §File-by-file. ✔
- Standalone preserved: R1 + "does not change" list. ✔
- Boot-readable role canon with mechanism named: §Seat loading (explicitly T-005-conditional
  with named fallback). ✔ conditional
- Gate: Codex review round 1 done (12 findings applied); targeted re-review + operator approval
  pending.
