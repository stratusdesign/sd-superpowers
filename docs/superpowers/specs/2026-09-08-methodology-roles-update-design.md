# Spec A — sd-superpowers Methodology Update: Role Cast + Foreman

> T-001 · Deliverable #1 · Status: draft, pending Codex review + operator approval.
> Boundary: this spec owns roles, authority, and skill changes (the WHO). Artifact formats are
> owned by Spec C (`2026-09-08-ticketing-doc-standard-design.md`) and are referenced by name only.
> Source of truth for rationale: `docs/superpowers/foreman-role-concept.md` (approved concept).

## Goal

Every session that boots into a foreman-supervised project sees the full role cast, knows its own
seat, and follows the seat's methodology — while sd-superpowers keeps working completely unchanged
for standalone (foreman-less) use.

## Design principle

All changes are **additive and seat-conditional**. No existing flow is rewired. A session with no
seat named behaves exactly as today; the repo's acceptance test ("Let's make a react todo list" →
brainstorming auto-triggers) must pass unchanged.

## The role cast (canonical definitions)

| Seat | Function | Authority domain |
|---|---|---|
| Operator | Owner | Plan changes, approvals, escalation terminus, acceptance |
| Strategic Advisor | Operator's thinking partner | Turns operator intent into documentation updates; no session control |
| Foreman | Orchestration (one per project) | Process + scope visibility: holds worker sessions, gates, commissioning, escalation; never authors domain solutions |
| SA / Architect | Technical delivery | Architecture, specs, tickets, doc accuracy, small direct fixes; decides content within the plan |
| Builder | Implementation | Executes tickets; appends Builder Reports; deviation notes, never spec edits |
| Checker | Independent judgment | One question per commission (scope drift, doc/code sync, review); fresh context; neutral brief |
| Hermes / Verifier | Evidence + spawning | Independent evidence source; spawns foremen; full agent, no normal worker contact |

Supporting rules carried verbatim from the concept: artifact rule (real = in a doc/ticket AND
acknowledged), raised ≠ approved with acceptance owners per finding class, neutral briefs
(artifacts + standing rubric, no advocacy), escalation ladder producer → foreman → operator.

## File-by-file changes

### 1. NEW `skills/role-cast/SKILL.md`
The role canon as a skill. Content: the cast table above, seat-resolution rule ("your seat is
named in your spawn brief; no seat named → standalone mode, this skill does not apply"), each
seat's obligations and prohibitions, the supporting rules. Trigger description: "Use when your
session brief names a seat (foreman, builder, SA, checker) or a foreman-supervised project."
- Standalone behavior: never triggers (no seat named).
- Foreman-present behavior: first skill invoked, before any process skill.

### 2. MOD `skills/using-superpowers/SKILL.md`
One addition to the Skill Priority section: "If your session brief names a seat, invoke
superpowers:role-cast before any other skill." No other changes; Red Flags table untouched.
- Standalone behavior: line is inert (no seat named).

### 3. MOD `skills/brainstorming/SKILL.md`
Add a short "Foreman-present mode" note to the two review gates (Design Review Gate, Written Spec
Review Gate): when a foreman supervises the project, reviewer commissioning and brief assembly are
requested THROUGH the foreman (the SA never dispatches its own reviewers); gate mechanics are
otherwise identical. Standalone: gates work exactly as today.

### 4. MOD `skills/writing-plans/SKILL.md`
Same pattern: plan-review commissioning goes through the foreman when present. One paragraph.

### 5. MOD `skills/subagent-driven-development/SKILL.md`
Two additions: (a) per-task and final reviews are foreman-commissioned when present; (b) builder
subagents follow the Builder Report standard (schema per Spec C, referenced by name).
Standalone: current self-orchestrated flow unchanged.

### 6. MOD `skills/requesting-code-review/SKILL.md`
One note: foreman-present → review request goes to the foreman, which owns reviewer choice and
brief; the requesting session supplies artifacts only.

### 7. NEW `skills/ticket-discipline/SKILL.md`
Vehicle for Spec C's schema (content defined there, not here). Trigger: working on a ticketed
project. Listed here only as an integration point.

## What does NOT change

using-superpowers Red Flags and rationalization tables; TDD; systematic-debugging;
verification-before-completion; finishing-a-development-branch; using-git-worktrees;
dispatching-parallel-agents; receiving-code-review; writing-skills; the PR/contribution rules.
The foreman is not implemented here — this spec only makes sessions foreman-literate.

## Seat loading mechanism

The spawn brief (message/system-prompt appended at session create) names the seat:
`Seat: builder · Ticket: T-012 · Project: <name>`. The SessionStart bootstrap already injects
using-superpowers; its new priority line routes seat-named sessions into role-cast. No hook
changes required. (Receipt: bootstrap injection verified live in operator infra; spawn-brief
delivery is the harness's job — Spec B.)

## Testing (per repo rules: skills are behavior, not prose)

- Regression: the repo acceptance test on a clean standalone session — brainstorming triggers,
  zero role content surfaces.
- New: session with a builder seat brief → role-cast invoked first, builder obligations stated,
  spec-section edit refused when asked.
- New: SA-seat session asked to "get this reviewed" → requests commissioning through foreman
  rather than dispatching a reviewer itself.
- Eval harness: one scenario per changed skill in `evals/` (drill), before/after documented in the
  PR per CONTRIBUTING rules.

## Acceptance criteria (from T-001)

- Every touched file listed with its behavioral change and both-mode behavior: above.
- Standalone fully preserved: regression test defined.
- Boot-readable role canon: role-cast skill + bootstrap priority line, mechanism named.
- Gate: Codex adversarial review, then operator approval.
