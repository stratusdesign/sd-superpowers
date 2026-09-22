---
name: ticket-discipline
description: Use when working on a ticketed project (a backlog.md + tickets/ directory exists), in any seat or standalone.
---

# Ticket Discipline

One schema for humans and scripts. Scripts parse only the exact syntax below; anything semantic is a reviewer judgment.

All prose values — summaries, results, deviations, receipts — follow the house standard: clear, factual, to the point; no unnecessary jargon; no wasteful sentences — each carries weight.

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
- **Closed contents list.** A ticket file contains ONLY: the header and status line (plus a `Parked: <reason>` line while parked), `## Spec reference`, `## Scope`, `## Acceptance criteria`, and `## Receipts`. Nothing else accumulates in the ticket — no inline reports, no essays, no restated review content. Detail lives in the files receipts point to. Tickets stay small by structure, not by intent.
- Pre-amendment tickets may still carry inline `## Builder Report` / `## Result` sections: read them as legacy, never imitate them.

## Done-claim reports (filed, never edited)

Done-claims are separate report files in `<project>/reports/`, one file per claim — never appended to the ticket:

- Build tickets: `reports/T-###-builder-report.md`; re-work files `reports/T-###-builder-report-2.md` etc.
- Experiment and spec tickets: `reports/T-###-result.md` (re-runs `-2` etc.).
- Build report format — first line is the heading `## Builder Report` (re-work: `## Builder Report (2)` etc.), then labeled single-line fields, each starting the line: `**Ticket:**` `**Status:**` `**Branch:**` `**Commit:**` `**Summary:**` `**Deviations from spec:**` `**Known issues:**`. Exactly one Deviations line; value `none` or prose. Long values continue on lines indented two spaces.
- Experiment and spec reports — first line is the heading `## Result`; experiment body is prose, spec body contains a `**Spec:** <path>` line. Either MAY carry a `**Deviations from spec:**` line; no line means none.
- Filing a done-claim means writing the report file AND appending its receipt to the ticket in the same pass. **The done-claim receipt's evidence-ref is the report path — mandatory, never substituted.** Its verdict field ends with `deviations: none` or `deviations: N`, where N counts the distinct deviations the report records (prose describing a single deviation = 1; no Deviations line = none). The foreman routes on this suffix from a tail poll without opening the report.
- Ambiguous or wrong spec → implement your best interpretation and record it as a deviation. Never block; never silently fix.

## Status lifecycle

| From | To | Trigger |
|---|---|---|
| open | in-progress | work dispatched |
| in-progress | review | done-claim report filed and its receipt appended |
| review | done | independent review passed, every finding dispositioned (applied, or rejected with reason recorded as a receipt), acknowledged |
| review | in-progress | review found material issues |
| open / in-progress / review | parked | deliberate deferral; add a `Parked: <reason>` line under the header |
| parked | open | revived (the only exit) |

`done` is terminal. No state skips — a done-claim never jumps `in-progress → done`. The receipt is what greps and tail polls see.

## Receipts

Ticket-scoped events append under a `## Receipts` section (created on first receipt), one line each, append-only:
`- <UTC ISO-8601> · <event> · <seat> · <verdict-or-action> · <evidence-ref>`
(`<evidence-ref>` = commit hash, file path, session id, or review-report pointer.)

**Receipt ceiling: 200 characters per line.** A receipt is timestamp · event · seat · verdict-plus-counts · evidence-ref — nothing more. Example:
`- <ts> · review-verdict · code-reviewer · FAIL, 5 major · reports/T-006-code-review-1.md`
Detail lives only in the referenced file; restating a report's content in a receipt is a format violation. A short reason (e.g. a finding rejection) that fits inside the ceiling lives in the verdict field; anything longer is filed and the receipt points to it.

**The timestamp is clock-sourced, never typed.** Produce it by command substitution inside the
same shell command that appends the line, so the model never writes the digits itself:
`printf -- "- %s · <event> · <seat> · <verdict> · <evidence>\n" "$(date -u +%Y-%m-%dT%H:%M:%SZ)" >> <ticket>`
A model-typed timestamp — however plausible, however close it looks to another file's mtime — is
never a valid receipt time. This is not a style preference: prose instructions ("use actual UTC
timestamps") have already failed in practice, including recurring inside the same pass meant to
correct it (tickets/T-018-validate-backend-model.md, sd-foreman project). Reviewer cross-checks
against this rule (claimed time vs. file mtime, vs. session activity records, future-dated) and
the accepted drift tolerance live wherever the commissioning project documents its review
commissioning (e.g. sd-foreman's `foreman/FOREMAN.md` §Completion) — this section is the format
authority; it does not restate those checks.

## Spawn brief

Line 1 is exactly `Seat: <seat> · Ticket: T-### · Project: <name>` and nothing else; free-form task text starts on line 2.

Where a project keeps a project-scope file (its operator-approved purpose, boundaries, and success conditions — the authoritative scope, changed only with operator sign-off), the brief points to it so the seat reads it rather than rediscovering scope.

## Machine anchors

- `T-###` appears in every commit message and every builder report's `**Ticket:**` field.
- Greppable in tickets, exact (POSIX ERE, `grep -E`): `^Status: ` · `^## Receipts` · receipt lines `^- [0-9]{4}-`.
- Greppable in `reports/` files, exact: `^## Builder Report` · `^## Result` · `^\*\*Deviations from spec:\*\*`. These anchors live in report files, never in post-amendment tickets.
- Receipt ceiling is mechanically checkable: `awk '/^- [0-9]{4}-/ && length($0)>200' <ticket>` — any output is a format violation.
- Backlog (`<project>/backlog.md`, table `| ID | Title | Deliverable | Status |`) is an index; the ticket header is authoritative — on divergence, flag, never resolve.

## Project documents

- Specs: `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`; plans: `docs/superpowers/plans/` (existing conventions, unchanged).
- SDD's per-task workspace reports (`.superpowers/sdd/<plan>/task-N-report.md`) are controller scratch, deleted with the workspace — they are NOT done-claim reports. A ticketed project's done-claim evidence lives in `reports/`, which survives.
- Every ticketed project carries `backlog.md`, `tickets/`, `reports/` (created on first done-claim), `PROCESS.md` **or inherits the methodology default** (a carried PROCESS.md holds the roles table binding each active seat to its holder — binding changes are plan changes), and a STATUS/handover doc for infra state.
