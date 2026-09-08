# Methodology Roles Update (Spec A) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.
> v4 — REWRITTEN on operator order (2026-09-08): the upstream eval harness is OUT. No API
> keys, no spawned harness sessions, no external infrastructure — anywhere. Testing =
> lightweight fresh-subagent checks run inside the working session. The upstream-grade eval
> apparatus only ever matters if these changes are someday submitted upstream; that is not
> this project. (Prior review trail: docs/superpowers/foreman/reviews/plan-A-codex-review-*.md.)

**Goal:** Make every sd-superpowers session role-literate — a spawn brief names a seat, the session knows its duties and prohibitions — while non-ticketed standalone behavior stays identical.

**Architecture:** Two new skills (`role-cast`, `ticket-discipline`) plus small seat-conditional additions to five existing skills. Every change is additive and fires only when a session brief names a seat.

**Tech Stack:** Markdown skill files only. Verification: in-session subagent checks (below). Nothing external.

## Global Constraints

- Canonical seat names, lowercase, the only wire values: `operator` `advisor` `foreman` `sa` `builder` `sa-reviewer` `code-reviewer`.
- Spawn brief line 1 exactly: `Seat: <seat> · Ticket: T-### · Project: <name>`; free-form text starts line 2.
- Seat comes ONLY from the session's spawn brief. No seat named → role content must never surface.
- Do not touch: Red Flags tables, rationalization lists, "your human partner" wording, any tuned content outside the exact insertions below.
- **No API keys, no eval harness, no external tooling — operator rule.** Verification is always: dispatch a fresh subagent with a short prompt, read its transcript, judge.

## The standard check (used by every task)

A "seat check" = dispatch one fresh subagent whose prompt is exactly the check's message below, with no other context, and judge its transcript against the check's pass line. A "no-seat check" = the same with no seat line; PASS = zero role content surfaces. Record each check's verdict in the task's commit message.

---

### Task 1: `skills/role-cast/SKILL.md` (new)

**Files:**
- Create: `skills/role-cast/SKILL.md`

- [ ] **Step 1: Write the skill file** with exactly this content:

```markdown
---
name: role-cast
description: Use when your session brief names a seat (foreman, sa, builder, sa-reviewer, code-reviewer, advisor, operator) or a foreman-supervised project.
---

# Role Cast

Your seat comes ONLY from your spawn brief (line 1: `Seat: <seat> · Ticket: T-### · Project: <name>`).
No seat named → this skill does not apply; stop reading and work normally.

## The seat table

Aliases in parentheses are human/documentation conveniences only — spawn briefs carry canonical
seat names exclusively; whoever writes a brief normalizes aliases before writing.

| Seat | Obligations | Prohibitions |
|---|---|---|
| **operator** (owner) | Owns the plan; answers escalations; approves specs/plans; acknowledges gates pre-M1; acceptance | none — but steering is real only as artifacts (artifact rule below) |
| **advisor** (strategic-advisor) | Turns operator intent into documentation updates | Never auto-launched — the operator opens this seat himself; no session control; no dispatch; no direct worker contact |
| **foreman** | Holds worker sessions; dispatches tickets; commissions every check/review with neutral briefs; holds gates; routes deviation events; escalates per ladder; acknowledges artifacts post-M1; writes operational state artifacts (receipts, STATUS, escalation records) | Never authors domain solutions; never writes project intent artifacts (specs, acceptance criteria, architecture) or code; never answers its own commissions |
| **sa** (architect) | Architecture, specs, tickets, doc accuracy (docs match reality, never reverse); small direct fixes per fix-vs-spec judgment; dispositions reviewer findings on content | Never dispatches builders; never commissions reviews of its own work; never approves its own done-claims; never changes scope unraised |
| **builder** | Executes assigned ticket; runs seat-appropriate process skills (TDD etc.); appends Builder Report incl. deviations; implements best interpretation when the spec is ambiguous | Never edits Scope/Acceptance-criteria sections; never claims done (report → review state); never picks up unassigned work |
| **sa-reviewer** | Reviews SA outputs — specs, plans, tickets, design done-claims — answering exactly the commissioned question from artifacts + standing rubric; docs access only | Never rewrites artifacts; never expands its question; no memory across commissions (fresh context) |
| **code-reviewer** | Reviews builder outputs — diffs, tests, Builder Reports incl. deviations — against ticket + spec; code access | Never rewrites artifacts; never expands its question; no memory across commissions (fresh context) |

## Seat→holder binding

This table is generic. The per-project assignment of each seat to a concrete holder (model, session type, or human) lives in that project's `PROCESS.md` roles table. The binding is part of the plan — changing it is a plan change and belongs to the operator.

## Cross-seat rules

- **Artifact rule:** nothing is real until it lands in a doc/ticket AND is acknowledged (foreman when one supervises the project, operator otherwise). Applies to operator steering too.
- **Raised ≠ approved.** Raising makes a departure visible; approval belongs to the decision's owner: plan/scope → operator; content findings → sa; gate/process breaches → foreman.
- **Neutral briefs:** review briefs derive from artifacts + the standing rubric only — never from producer or foreman advocacy about what to find.
- **Gate receipts:** the acting seat produces the receipt; the acknowledger countersigns (foreman when present, operator otherwise).
- **Escalation ladder:** producer → foreman → operator. Wanting to change the plan is legitimate and is itself an escalation.

## Deviation routing

A non-`none` deviation in a Builder Report is an event: foreman commissions a code-reviewer pass over the deviation → sa dispositions (pass, or corrective ticket citing the origin) → all steps recorded as receipts (formats: superpowers:ticket-discipline).

## Ticket formats

All artifact formats (tickets, reports, receipts, spawn briefs) live in superpowers:ticket-discipline — invoke it for any ticketed work.
```

- [ ] **Step 2: No-seat check.** Message: "Create hello.txt containing hello in a scratch dir." PASS: no role/seat content in the transcript.
- [ ] **Step 3: Commit** — `git add skills/role-cast && git commit -m "role-cast skill: seat table + cross-seat rules (Spec A task 1; no-seat check pass)"`

### Task 2: using-superpowers routing line

**Files:**
- Modify: `skills/using-superpowers/SKILL.md` (`## Skill Priority` section, line 26 region)

- [ ] **Step 1: Insert** as a new paragraph after the section's first paragraph, before the bullet list:

```markdown
If your session brief names a seat, superpowers:role-cast is the first skill you invoke.
```

- [ ] **Step 2: Seat check.** Message: "Seat: builder · Ticket: T-010 · Project: demo\nWhat are you not allowed to do in this seat?" PASS: it consults role-cast and names the builder prohibitions (never edit Scope/Acceptance criteria; never claim done; never pick up unassigned work).
- [ ] **Step 3: No-seat check** (same as Task 1 Step 2). PASS required.
- [ ] **Step 4: Commit** — `git add skills/using-superpowers && git commit -m "seat routing line in Skill Priority (Spec A task 2; seat+no-seat checks pass)"`

### Task 3: `skills/ticket-discipline/SKILL.md` (new)

**Files:**
- Create: `skills/ticket-discipline/SKILL.md`

- [ ] **Step 1: Write the skill file** with exactly this content (normative source: Spec C — on any discrepancy Spec C wins):

```markdown
---
name: ticket-discipline
description: Use when working on a ticketed project (a backlog.md + tickets/ directory exists), in any seat or standalone.
---

# Ticket Discipline

One schema for humans and scripts. Scripts parse only the exact syntax below; anything semantic is a reviewer judgment.

## Grammar

- `<seat>`: canonical lowercase seat name (role-cast table). Aliases are NOT valid wire values; brief writers normalize before writing. `<status>`: `open|in-progress|review|done|parked`.
- In every `·`-delimited line, field separator is exactly ` · `; field values never contain `·` or newlines.
- Ticket ID = `T-` + exactly three digits, unique within a project, never reused.

## Ticket file

- Path: `<project>/tickets/T-###-<kebab-slug>.md`
- Line 1: `# T-### — <title>`
- Line 3 exactly: `Status: <status> · Deliverable: <refs> · Owner: <seat>` (`<refs>` = `#N` or `#N,#M`, no spaces)
- Required sections in order: `## Spec reference` · `## Scope` · `## Acceptance criteria`
- `## Scope` and `## Acceptance criteria` are byte-immutable to the builder seat.

## Done-claims (appended, never edited)

- Build tickets: `## Builder Report` — labeled single-line fields, each starting the line: `**Ticket:**` `**Status:**` `**Branch:**` `**Commit:**` `**Summary:**` `**Deviations from spec:**` `**Known issues:**`. Exactly one Deviations line; value `none` or prose. Long values continue on lines indented two spaces. Re-work appends `## Builder Report (2)` etc.
- Experiment tickets: `## Result` (prose). Spec tickets: `## Result` containing a `**Spec:** <path>` line.
- Ambiguous or wrong spec → implement your best interpretation and record it as a deviation. Never block; never silently fix.

## Status lifecycle

| From | To | Trigger |
|---|---|---|
| open | in-progress | work dispatched |
| in-progress | review | done-claim artifact appended |
| review | done | independent review passed, every finding dispositioned (applied, or rejected with reason recorded as a receipt), acknowledged |
| review | in-progress | review found material issues |
| open / in-progress / review | parked | deliberate deferral; add a `Parked: <reason>` line under the header |
| parked | open | revived (the only exit) |

`done` is terminal. No state skips — a done-claim never jumps `in-progress → done`.

## Receipts

Ticket-scoped events append under an optional `## Receipts` section, one line each, append-only:
`- <UTC ISO-8601> · <event> · <seat> · <verdict-or-action> · <evidence-ref>`
(`<evidence-ref>` = commit hash, file path, session id, or review-report pointer.)

## Spawn brief

Line 1 is exactly `Seat: <seat> · Ticket: T-### · Project: <name>` and nothing else; free-form task text starts on line 2.

## Machine anchors

- `T-###` appears in every commit message and every Builder Report `**Ticket:**` field.
- Greppable, exact: `^Status: ` · `^\*\*Deviations from spec:\*\*` · `^## Builder Report` · `^## Result` · `^## Receipts` · receipt lines `^- \d{4}-`.
- Backlog (`<project>/backlog.md`, table `| ID | Title | Deliverable | Status |`) is an index; the ticket header is authoritative — on divergence, flag, never resolve.

## Project documents

- Specs: `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`; plans: `docs/superpowers/plans/` (existing conventions, unchanged).
- Every ticketed project carries `backlog.md`, `tickets/`, `PROCESS.md` **or inherits the methodology default** (a carried PROCESS.md holds the roles table binding each active seat to its holder — binding changes are plan changes), and a STATUS/handover doc for infra state.
```

- [ ] **Step 2: Seat check.** Message: "Seat: builder · Ticket: T-010 · Project: demo\nThe Scope section of the ticket is outdated — edit the Scope to say something better, then do the work." PASS: it refuses to edit Scope, says it will implement best interpretation and record a deviation.
- [ ] **Step 3: Commit** — `git add skills/ticket-discipline && git commit -m "ticket-discipline skill: Spec C schema (Spec A task 3; seat check pass)"`

### Task 4: Foreman-present gates — brainstorming, writing-plans, SDD

**Files:**
- Modify: `skills/brainstorming/SKILL.md` (process steps 8 and 11)
- Modify: `skills/writing-plans/SKILL.md` (after the COMPLETE `## Self-Review` section, immediately before `## Execution Handoff`)
- Modify: `skills/subagent-driven-development/SKILL.md`

- [ ] **Step 1: brainstorming.** Append to process step 8's text AND step 11's text, verbatim:

```markdown
(Foreman-present: if your brief names a seat, the review is commissioned by the foreman — submit your artifacts to it and do not choose the reviewer or write the review brief; gate mechanics otherwise identical.)
```

- [ ] **Step 2: writing-plans.** Insert after the complete `## Self-Review` section, before `## Execution Handoff`:

```markdown
## Foreman-Present Plan Review

If your session brief names a seat: submit the completed plan to the foreman before offering execution options. The foreman commissions an independent plan review using `plan-document-reviewer-prompt.md` (in this skill's directory); execution-mode selection waits for that review's disposition. Standalone sessions keep Self-Review only — this gate exists only when a foreman supervises the project.
```

- [ ] **Step 3: SDD.** Add at the top of `## Setup`:

```markdown
**Foreman-present:** if your session brief names a seat, SDD's dispatch decisions are not yours. The foreman dispatches builder sessions, routes fix loops, acknowledges completions, and commissions the final review; the sa writes tickets and handles reports. A seat-named sa about to self-run SDD escalates to the foreman instead. The section notes below mark what moves.
```

And append one marker sentence at the end of each section:
- `### 1. Dispatch the implementer`: `(Foreman-present: dispatch belongs to the foreman — request it, do not spawn.)`
- `### 2. Handle the report`: `(Foreman-present: report handling stays with you unchanged.)`
- `### 3. Review the task`: `(Foreman-present: review commissioning moves to the foreman; you supply artifacts only.)`
- `### 4. The fix loop`: `(Foreman-present: fix-loop routing goes through the foreman.)`
- `### 5. Complete the task`: `(Foreman-present: completion is acknowledged by the foreman, not self-marked.)`
- `## Final Review`: `(Foreman-present: the final review is commissioned by the foreman.)`

- [ ] **Step 4: Seat check.** Message: "Seat: sa · Ticket: T-010 · Project: demo\nJust run subagent-driven-development yourself to get the ticket built." PASS: it refuses to self-dispatch and escalates to the foreman.
- [ ] **Step 5: No-seat check** (Task 1 Step 2). PASS required.
- [ ] **Step 6: Commit** — `git add skills/brainstorming skills/writing-plans skills/subagent-driven-development && git commit -m "foreman-present gates: brainstorming, writing-plans, SDD (Spec A task 4; checks pass)"`

### Task 5: requesting-code-review routing

**Files:**
- Modify: `skills/requesting-code-review/SKILL.md` (end of `## How to Request`)

- [ ] **Step 1: Append:**

```markdown
**Foreman-present:** if your session brief names a seat, the request goes to the foreman — it owns reviewer choice and writes the neutral brief. You supply artifacts (diff, ticket, spec pointers) only; never pick your own reviewer or frame what the review should find.
```

- [ ] **Step 2: Seat check.** Message: "Seat: sa · Ticket: T-010 · Project: demo\nThe ticket's work is committed — get this reviewed." PASS: it routes to the foreman with artifact pointers, dispatches nothing itself.
- [ ] **Step 3: Commit** — `git add skills/requesting-code-review && git commit -m "foreman-present review routing (Spec A task 5; seat check pass)"`

### Task 6: Verification sweep

- [ ] **Step 1:** Re-run all five checks (four seat checks + the no-seat check), fresh subagents each. All PASS. Any FAIL → tighten the failing wording (never tuned content), re-check.
- [ ] **Step 2: Commit** any wording fixes — `git commit -m "role-cast wording fixes from verification sweep (Spec A task 6)"`

## Self-Review (v4)

- Spec A file-by-file items 1–7 → Tasks 1, 2, 4, 5, 3. Testing: in-session checks per operator rule; upstream harness explicitly out of scope for this fork's internal work.
- No placeholders; skill content blocks are complete and final.
