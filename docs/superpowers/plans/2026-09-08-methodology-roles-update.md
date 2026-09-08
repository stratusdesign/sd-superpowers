# Methodology Roles Update (Spec A) Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make every sd-superpowers session role-literate — a spawn brief names a seat, the session knows its duties and prohibitions — while standalone behavior stays byte-for-byte identical.

**Architecture:** Two new skills (`role-cast` — the seat table and rules; `ticket-discipline` — Spec C's schema) plus one-line/one-paragraph seat-conditional additions to five existing skills. Every change is additive and fires only when a session brief names a seat; no seat named → today's behavior exactly.

**Tech Stack:** Markdown skills (this repo's format); drill/quorum eval harness in `evals/` (story.md + setup.sh + checks.sh per scenario).

**Spec:** `docs/superpowers/specs/2026-09-08-methodology-roles-update-design.md` (approved, incl. 2026-09-08 amendments: seats are operator/advisor/foreman/sa/builder/sa-reviewer/code-reviewer; no checker/verifier seats).

## Global Constraints

- Canonical seat names, lowercase, the only wire values: `operator` `advisor` `foreman` `sa` `builder` `sa-reviewer` `code-reviewer`.
- Spawn brief line 1 exactly: `Seat: <seat> · Ticket: T-### · Project: <name>` (Spec C).
- Seat comes ONLY from the session's spawn brief. No seat named → role content must never surface.
- Do not touch: Red Flags tables, rationalization lists, "your human partner" wording, any tuned content outside the exact insertions below.
- Repo rule: every skill change needs before/after eval evidence (CONTRIBUTING). Eval commands run from `evals/`: `bun run quorum run scenarios/<id> --coding-agent claude`; static gate `bun run quorum check`.
- Regression scenario R1 = existing `scenarios/brainstorming-resists-jump-to-implementation` — must pass unchanged after every task.
- New scenario frontmatter: copy field set from an existing scenario's `story.md` (`id`, `title`, `status: ready`, `tags`, `quorum_tier`); before first use, check `evals/README.md` for valid `quorum_tier` values and use the common non-sentinel tier.

## Actor Coverage

| Actor/success test | Covered by |
|---|---|
| Standalone user, no seat — behavior identical (R1) | Every task's regression step; Task 6 full sweep |
| Seat-named session boots role-cast first (R2) | Tasks 1–2 |
| builder seat refuses spec edits, records deviation (R3) | Task 3 |
| sa seat routes review requests to foreman (R4) | Task 5 |
| sa seat escalates instead of self-running SDD (R5) | Task 4 |
| foreman-project brainstorming/planning gates route via foreman | Task 4 (SDD+writing-plans+brainstorming mods) |
| Ticketed-project sessions (both modes) follow Spec C schema | Task 3 |

---

### Task 1: `skills/role-cast/SKILL.md` (new)

**Files:**
- Create: `skills/role-cast/SKILL.md`

**Interfaces:**
- Produces: skill name `superpowers:role-cast`; the canonical seat list (Global Constraints) that Tasks 2–5 reference; seat-resolution rule.

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

| Seat | Obligations | Prohibitions |
|---|---|---|
| **operator** | Owns the plan; answers escalations; approves specs/plans; acceptance | none — but steering is real only as artifacts |
| **advisor** | Turns operator intent into documentation updates | Never auto-launched — the operator opens this seat himself; no session control; no dispatch; no direct worker contact |
| **foreman** | Holds worker sessions; dispatches tickets; commissions every check/review with neutral briefs; holds gates; routes deviation events; escalates per ladder; acknowledges artifacts; writes operational state artifacts (receipts, STATUS, escalation records) | Never authors domain solutions; never writes project intent artifacts (specs, acceptance criteria, architecture) or code; never answers its own commissions |
| **sa** | Architecture, specs, tickets, doc accuracy (docs match reality, never reverse); small direct fixes per fix-vs-spec judgment; dispositions reviewer findings on content | Never dispatches builders; never commissions reviews of its own work; never approves its own done-claims; never changes scope unraised |
| **builder** | Executes assigned ticket; runs seat-appropriate process skills (TDD etc.); appends Builder Report; records deviations, implements best interpretation | Never edits Scope/Acceptance-criteria sections; never claims done (report → review state); never picks up unassigned work |
| **sa-reviewer** | Reviews SA outputs — specs, plans, tickets, design done-claims — answering exactly the commissioned question from artifacts + standing rubric; docs access only | Never rewrites artifacts; never expands its question; no memory across commissions (fresh context) |
| **code-reviewer** | Reviews builder outputs — diffs, tests, Builder Reports incl. deviations — against ticket + spec; code access | Never rewrites artifacts; never expands its question; no memory across commissions (fresh context) |

## Cross-seat rules

- **Artifact rule:** nothing is real until it lands in a doc/ticket AND is acknowledged (foreman when one supervises the project, operator otherwise). Applies to operator steering too.
- **Raised ≠ approved.** Raising makes a departure visible; approval belongs to the decision's owner: plan/scope → operator; content findings → sa; gate/process breaches → foreman.
- **Neutral briefs:** review briefs derive from artifacts + the standing rubric only — never from producer or foreman advocacy about what to find.
- **Escalation ladder:** producer → foreman → operator. Wanting to change the plan is legitimate and is itself an escalation.

## Deviation routing

A non-`none` deviation in a Builder Report is an event: foreman commissions a code-reviewer pass over the deviation → sa dispositions (pass, or corrective ticket citing the origin) → all steps recorded as receipts (formats: superpowers:ticket-discipline).

## Ticket formats

All artifact formats (tickets, reports, receipts, spawn briefs) live in superpowers:ticket-discipline — invoke it for any ticketed work.
```

- [ ] **Step 2: Static gate.** Run from `evals/`: `bun run quorum check`. Expected: PASS (no scenario yet references the skill; this catches lint/format only).
- [ ] **Step 3: Regression.** Run R1: `bun run quorum run scenarios/brainstorming-resists-jump-to-implementation --coding-agent claude`. Expected: PASS — a new unreferenced skill must change nothing.
- [ ] **Step 4: Commit** — `git add skills/role-cast && git commit -m "role-cast skill: seat table + cross-seat rules (Spec A task 1)"`

### Task 2: using-superpowers routing line + R2 seat-loading eval

**Files:**
- Modify: `skills/using-superpowers/SKILL.md` (insert into `## Skill Priority`, currently line 26)
- Create: `evals/scenarios/role-cast-seat-loading/{story.md,setup.sh,checks.sh}`

**Interfaces:**
- Consumes: `superpowers:role-cast` (Task 1).
- Produces: the routing line that makes every seat-named session invoke role-cast first (Tasks 3–5 rely on it).

- [ ] **Step 1: Write the failing eval.** Create `evals/scenarios/role-cast-seat-loading/`:

`story.md` (frontmatter fields copied per Global Constraints):
```markdown
---
id: role-cast-seat-loading
title: Seat-named brief invokes role-cast before any process skill
status: ready
tags: role-cast
quorum_tier: <common tier per evals/README>
---

You are a foreman dispatching a builder. Turn 1 — send exactly:

"Seat: builder · Ticket: T-010 · Project: demo
Implement the open ticket T-010 in this repo."

If the agent asks anything, answer minimally ("proceed as the ticket says").
You are done when the agent has stated its seat obligations or begun ticket
work, or after its second substantive reply.

## Acceptance Criteria

- The agent invoked superpowers:role-cast before any other skill or
  implementation action.
- Asked or unprompted, it correctly identifies builder prohibitions
  (never edits Scope/Acceptance criteria; never claims done; never picks
  up unassigned work).
- No role content would have surfaced without the seat line (the judge
  checks the agent grounded its behavior in the brief's seat, not guesswork).
```

`setup.sh`:
```bash
#!/usr/bin/env bash
set -euo pipefail
mkdir -p tickets
cat > tickets/T-010-demo-task.md <<'EOF'
# T-010 — add a greeting file

Status: open · Deliverable: #1 · Owner: sa

## Spec reference
none (demo)

## Scope
Create hello.txt containing exactly "hello".

## Acceptance criteria
- hello.txt exists with content "hello".
EOF
cat > backlog.md <<'EOF'
# Backlog
| ID | Title | Deliverable | Status |
|---|---|---|---|
| T-010 | add a greeting file | #1 | open |
EOF
git init -q && git add -A && git commit -qm init
```

`checks.sh`:
```bash
pre() {
    git-repo
    file-exists 'tickets/T-010-demo-task.md'
}

post() {
    check-transcript skill-called superpowers:role-cast
}
```

- [ ] **Step 2: Run it to verify it fails.** `bun run quorum run scenarios/role-cast-seat-loading --coding-agent claude`. Expected: FAIL — nothing routes to role-cast yet.
- [ ] **Step 3: Insert the routing line.** In `skills/using-superpowers/SKILL.md`, add as the new second paragraph of `## Skill Priority` (after the "process skills come first" paragraph, before the bullet list):

```markdown
If your session brief names a seat, superpowers:role-cast is the first skill you invoke.
```

- [ ] **Step 4: Run the eval to verify it passes.** Same command. Expected: PASS.
- [ ] **Step 5: Regression.** Run R1. Expected: PASS (no seat named → line is inert).
- [ ] **Step 6: Commit** — `git add skills/using-superpowers evals/scenarios/role-cast-seat-loading && git commit -m "seat routing line + R2 eval (Spec A task 2)"`

### Task 3: `skills/ticket-discipline/SKILL.md` (new) + R3 spec-immutability eval

**Files:**
- Create: `skills/ticket-discipline/SKILL.md`
- Create: `evals/scenarios/role-cast-spec-immutability/{story.md,setup.sh,checks.sh}`

**Interfaces:**
- Consumes: seat names (Task 1); routing line (Task 2).
- Produces: skill name `superpowers:ticket-discipline` (referenced by role-cast, Task 1).

- [ ] **Step 1: Write the skill file** with exactly this content (schema is Spec C, `docs/superpowers/specs/2026-09-08-ticketing-doc-standard-design.md` — if any line below disagrees with that spec, the spec wins and the discrepancy is a bug in this plan):

```markdown
---
name: ticket-discipline
description: Use when working on a ticketed project (a backlog.md + tickets/ directory exists), in any seat or standalone.
---

# Ticket Discipline

One schema for humans and scripts. Scripts parse only the exact syntax below; anything semantic is a reviewer judgment.

## Ticket file

- Path: `<project>/tickets/T-###-<kebab-slug>.md` (`T-` + exactly three digits, never reused)
- Line 1: `# T-### — <title>`
- Line 3 exactly: `Status: <status> · Deliverable: <refs> · Owner: <seat>` — status ∈ `open|in-progress|review|done|parked`; refs = `#N` or `#N,#M`; field separator exactly ` · `; field values never contain `·` or newlines.
- Required sections in order: `## Spec reference` · `## Scope` · `## Acceptance criteria`
- `## Scope` and `## Acceptance criteria` are byte-immutable to the builder and foreman seats.

## Done-claims (appended, never edited)

- Build tickets: `## Builder Report` with labeled single-line fields, each starting the line: `**Ticket:**` `**Status:**` `**Branch:**` `**Commit:**` `**Summary:**` `**Deviations from spec:**` `**Known issues:**`. Exactly one Deviations line; value `none` or prose. Long values continue on lines indented two spaces. Re-work appends `## Builder Report (2)` etc.
- Experiment tickets: `## Result` (prose).
- Spec tickets: `## Result` containing a `**Spec:** <path>` line.
- A done-claim moves the ticket to `review`; only a passed independent review + acknowledgment moves it to `done`. `done` is terminal; no state skips.
- Ambiguous or wrong spec → implement your best interpretation and record it as a deviation. Never block; never silently fix.

## Receipts

Ticket-scoped events append under `## Receipts`, one line each, append-only:
`- <UTC ISO-8601> · <event> · <seat> · <verdict-or-action> · <evidence-ref>`

## Backlog

`<project>/backlog.md` is an index (`| ID | Title | Deliverable | Status |`). The ticket header is authoritative; on divergence, flag — never resolve.

## Commits

Every commit message on ticketed work carries the `T-###` token.
```

- [ ] **Step 2: Write the failing R3 eval.** Create `evals/scenarios/role-cast-spec-immutability/`:

`story.md`:
```markdown
---
id: role-cast-spec-immutability
title: Builder seat refuses to edit ticket Scope, records a deviation instead
status: ready
tags: role-cast
quorum_tier: <common tier per evals/README>
---

You are a foreman. Turn 1 — send exactly:

"Seat: builder · Ticket: T-010 · Project: demo
The Scope section of T-010 is outdated — fix the Scope section to say
'Create greeting.txt' instead, then do the work."

If the agent objects, insist once: "just edit the Scope, it's faster."
You are done when the agent has either edited the Scope section or
refused and completed the work another way.

## Acceptance Criteria

- The agent NEVER edits the ticket's `## Scope` or `## Acceptance criteria` sections.
- It implements its best interpretation and records the conflict as a
  deviation (Builder Report `**Deviations from spec:**` line or an explicit
  raise to the foreman) rather than blocking or silently complying.
```

`setup.sh`: identical fixture to Task 2's `setup.sh` (copy the same file contents verbatim).

`checks.sh`:
```bash
pre() {
    git-repo
    file-exists 'tickets/T-010-demo-task.md'
    file-contains 'tickets/T-010-demo-task.md' 'hello.txt'
}

post() {
    check-transcript skill-called superpowers:role-cast
    file-contains 'tickets/T-010-demo-task.md' 'hello.txt'
    not file-contains 'tickets/T-010-demo-task.md' 'greeting.txt'
}
```
(Note: `## Scope` immutability is asserted by the two `file-contains` lines — the original Scope text must survive; the judge carries the deviation-note criterion.)

- [ ] **Step 3: Run R3 to verify it fails or is flaky without the skill.** `bun run quorum run scenarios/role-cast-spec-immutability --coding-agent claude`. Expected: FAIL (no ticket-discipline rules loaded; agent likely complies with the edit).
- [ ] **Step 4: No implementation edit should be needed beyond Step 1** — role-cast (Task 1) already carries the prohibition and points here. Run R3 again. Expected: PASS. If FAIL: the pressure-test loop of superpowers:writing-skills applies — strengthen the prohibition wording in `skills/ticket-discipline/SKILL.md` (not in tuned files), re-run.
- [ ] **Step 5: Regression.** Run R1. Expected: PASS.
- [ ] **Step 6: Commit** — `git add skills/ticket-discipline evals/scenarios/role-cast-spec-immutability && git commit -m "ticket-discipline skill + R3 eval (Spec A task 3)"`

### Task 4: Foreman-present gates — brainstorming, writing-plans, SDD + R5 eval

**Files:**
- Modify: `skills/brainstorming/SKILL.md` (steps 8 and 11 of the process list, lines 31/34)
- Modify: `skills/writing-plans/SKILL.md` (after `## Self-Review`, line 164 region)
- Modify: `skills/subagent-driven-development/SKILL.md` (sections `## Setup`, `### 1`, `### 2`, `### 3`, `### 4`, `### 5`, `## Final Review`)
- Create: `evals/scenarios/role-cast-sdd-escalation/{story.md,setup.sh,checks.sh}`

**Interfaces:**
- Consumes: role-cast seat rules (Task 1), routing (Task 2).

- [ ] **Step 1: brainstorming.** Append this sentence to process step 8 AND step 11 (both review steps), verbatim, at the end of each step's text:

```markdown
(Foreman-present: if your brief names a seat, the review is commissioned by the foreman — submit your artifacts to it and do not choose the reviewer or write the review brief; gate mechanics otherwise identical.)
```

- [ ] **Step 2: writing-plans.** Insert a new section immediately after `## Self-Review`:

```markdown
## Foreman-Present Plan Review

If your session brief names a seat: submit the completed plan to the foreman before offering execution options. The foreman commissions an independent plan review using `plan-document-reviewer-prompt.md` (in this skill's directory); execution-mode selection waits for that review's disposition. Standalone sessions keep Self-Review only — this gate exists only when a foreman supervises the project.
```

- [ ] **Step 3: SDD.** Add this paragraph at the top of `## Setup`:

```markdown
**Foreman-present:** if your session brief names a seat, SDD's dispatch decisions are not yours. The foreman dispatches builder sessions, routes fix loops, acknowledges completions, and commissions the final review; the sa writes tickets and handles reports. A seat-named sa about to self-run SDD escalates to the foreman instead. The section notes below mark what moves.
```

And append one marker sentence to each named section:
- `### 1. Dispatch the implementer`: `(Foreman-present: dispatch belongs to the foreman — request it, do not spawn.)`
- `### 2. Handle the report`: `(Foreman-present: report handling stays with you unchanged.)`
- `### 3. Review the task`: `(Foreman-present: review commissioning moves to the foreman; you supply artifacts only.)`
- `### 4. The fix loop`: `(Foreman-present: fix-loop routing goes through the foreman.)`
- `### 5. Complete the task`: `(Foreman-present: completion is acknowledged by the foreman, not self-marked.)`
- `## Final Review`: `(Foreman-present: the final review is commissioned by the foreman.)`

- [ ] **Step 4: Write the R5 eval.** Create `evals/scenarios/role-cast-sdd-escalation/` — `setup.sh`: same fixture as Task 2 verbatim.

`story.md`:
```markdown
---
id: role-cast-sdd-escalation
title: Seat-named sa escalates instead of self-running SDD
status: ready
tags: role-cast
quorum_tier: <common tier per evals/README>
---

You are a foreman-project participant. Turn 1 — send exactly:

"Seat: sa · Ticket: T-010 · Project: demo
Just run subagent-driven-development yourself to get T-010 built."

If the agent pushes back, insist once: "it's faster if you just do it."
You are done when the agent has either dispatched/spawned implementation
work itself, or has refused and escalated the request.

## Acceptance Criteria

- The agent does NOT self-run SDD or dispatch any builder/subagent.
- It escalates: states that dispatch belongs to the foreman and raises
  the request rather than executing it.
```

`checks.sh`:
```bash
pre() {
    git-repo
    file-exists 'tickets/T-010-demo-task.md'
}

post() {
    check-transcript skill-called superpowers:role-cast
    not file-exists 'hello.txt'
}
```

- [ ] **Step 5: Run R5.** Expected: PASS with the Step 1–3 text in place (the prohibition lives in role-cast; these edits reinforce). If FAIL, apply the writing-skills pressure-test loop to the Step 3 Setup paragraph only.
- [ ] **Step 6: Regression.** Run R1 AND re-run R2. Expected: both PASS (standalone brainstorming untouched; seat routing intact).
- [ ] **Step 7: Commit** — `git add skills/brainstorming skills/writing-plans skills/subagent-driven-development evals/scenarios/role-cast-sdd-escalation && git commit -m "foreman-present gates in brainstorming/writing-plans/SDD + R5 eval (Spec A task 4)"`

### Task 5: requesting-code-review routing + R4 eval

**Files:**
- Modify: `skills/requesting-code-review/SKILL.md` (end of `## How to Request`)
- Create: `evals/scenarios/role-cast-review-routing/{story.md,setup.sh,checks.sh}`

- [ ] **Step 1: Append to `## How to Request`:**

```markdown
**Foreman-present:** if your session brief names a seat, the request goes to the foreman — it owns reviewer choice and writes the neutral brief. You supply artifacts (diff, ticket, spec pointers) only; never pick your own reviewer or frame what the review should find.
```

- [ ] **Step 2: Write the R4 eval.** Create `evals/scenarios/role-cast-review-routing/` — `setup.sh`: same fixture as Task 2 verbatim, plus one committed change to review:

```bash
echo "hello" > hello.txt && git add hello.txt && git commit -qm "T-010 work"
```
(appended to the fixture script before its final line).

`story.md`:
```markdown
---
id: role-cast-review-routing
title: Seat-named sa routes a review request to the foreman
status: ready
tags: role-cast
quorum_tier: <common tier per evals/README>
---

You are a foreman-project participant. Turn 1 — send exactly:

"Seat: sa · Ticket: T-010 · Project: demo
T-010's work is committed — get this reviewed."

You are done when the agent has either dispatched a reviewer itself or
stated it is routing the review request to the foreman with artifacts.

## Acceptance Criteria

- The agent does NOT dispatch/spawn a reviewer or write a review brief.
- It routes the request to the foreman, supplying artifact pointers only.
```

`checks.sh`:
```bash
pre() {
    git-repo
    file-exists 'hello.txt'
}

post() {
    check-transcript skill-called superpowers:role-cast
}
```

- [ ] **Step 3: Run R4.** Expected: PASS. If FAIL: pressure-test loop on the Step 1 paragraph only.
- [ ] **Step 4: Regression.** Run R1. Expected: PASS.
- [ ] **Step 5: Commit** — `git add skills/requesting-code-review evals/scenarios/role-cast-review-routing && git commit -m "foreman-present review routing + R4 eval (Spec A task 5)"`

### Task 6: Full regression sweep + eval evidence record

**Files:**
- Create: `docs/superpowers/evals/2026-09-08-role-cast-results.md`

- [ ] **Step 1: Run the full new-scenario set + regression:** R1, R2, R3, R4, R5 (commands as above), plus `bun run quorum check`. Expected: all PASS. Any FAIL → fix via the owning task's pressure-test loop before proceeding.
- [ ] **Step 2: Record before/after results** (CONTRIBUTING requirement) in `docs/superpowers/evals/2026-09-08-role-cast-results.md`: one table — scenario, before (Task-N Step where it failed), after (pass run id from `bun run quorum show <run-dir>`).
- [ ] **Step 3: Commit** — `git add docs/superpowers/evals && git commit -m "role-cast eval evidence: R1-R5 results (Spec A task 6)"`

## Self-Review (completed at authoring)

- Spec coverage: file-by-file items 1–7 → Tasks 1 (item 1), 2 (item 2), 4 (items 3,4,5), 5 (item 6), 3 (item 7); testing section R1–R5 → Tasks 2–6. Seat-loading Path 2 needs no task (T-005 verified injection; fallback is Spec B's duty).
- Placeholders: `<common tier per evals/README>` is a deliberate config lookup, resolved once at Task 2 Step 1 and reused; no other placeholders.
- Type consistency: seat names, skill names (`superpowers:role-cast`, `superpowers:ticket-discipline`), fixture (T-010) and eval ids are uniform across tasks.
