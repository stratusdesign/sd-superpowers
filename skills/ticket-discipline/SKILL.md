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
