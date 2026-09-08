# Spec C — Documentation + Ticketing Standard

> T-002 · Deliverable #1 · Status: draft v2 (post Codex review 1) · pending targeted re-review +
> operator approval.
> Boundary: this spec owns artifact formats and lifecycle (WHAT). Duties are Spec A's and are
> referenced only. Baseline: PulseMark contract (verified on disk).

## Goal

One schema serving humans and deterministic scripts. Scripts parse only explicit syntax defined
here; anything semantic is a checker judgment (concept: Script vs model).

## Grammar conventions

- `<seat>` = a **canonical** seat name from Spec A's table (lowercase). Aliases are NOT valid
  wire values; brief writers normalize before writing.
- `<status>` = `open|in-progress|review|done|parked`.
- In EVERY `·`-delimited line this spec defines (headers, receipt lines, spawn briefs), field
  values MUST NOT contain `·` or newlines. Field separator is exactly ` · `.
- Ticket ID = `T-` + exactly three digits (`T-\d{3}`), unique within a project, never reused.
  v1 is single-project; cross-project namespacing is deferred to Spec B if ever needed.

## Ticket file (normative)

- Path: `<project>/tickets/T-###-<kebab-slug>.md`
- Line 1: `# T-### — <title>`
- Line 3 (exact): `Status: <status> · Deliverable: <refs> · Owner: <seat>`
  where `<refs>` = `#N` or comma-list `#N,#M` (no spaces).
- Required sections in order: `## Spec reference` · `## Scope` · `## Acceptance criteria`
- `## Scope` and `## Acceptance criteria` are byte-immutable to the builder seat (duty: Spec A).
- Completion artifact, appended:
  - Build tickets: `## Builder Report` — labeled single-line fields, each starting the line:
    `**Ticket:**` (value `T-###`) `**Status:**` `**Branch:**` `**Commit:**` `**Summary:**`
    `**Deviations from spec:**` `**Known issues:**`. Continuation of a long value: following lines indented two spaces.
    **Exactly one** `**Deviations from spec:**` line per report; value is `none` or prose.
    Re-work appends a numbered `## Builder Report (2)` etc. — reports are never edited.
  - Experiment tickets: `## Result` (same review gate; prose body).
  - Spec tickets: the produced spec document is the done-claim; the ticket appends `## Result`
    containing a `**Spec:** <path>` line (+ review-round notes). Entering `review` requires that
    section, same as any done-claim.
- Optional `## Receipts` section — see §Receipts.

## Backlog index (normative)

- Path: `<project>/backlog.md`; per-phase table `| ID | Title | Deliverable | Status |`.
- Index only. **The ticket header is authoritative**; scripts flag backlog/ticket divergence as
  an error rather than resolving it.

## Status lifecycle

| From | To | Trigger |
|---|---|---|
| open | in-progress | work dispatched (duty: Spec A) |
| in-progress | review | done-claim artifact appended (Report/Result) |
| review | done | independent review passed, every finding **dispositioned** (applied, or rejected with reason recorded as a receipt), **acknowledged** (acknowledger per Spec A: foreman when present, operator otherwise) |
| review | in-progress | review found material issues |
| open / in-progress / review | parked | deliberate deferral; a `Parked: <reason>` line added under the header |
| parked | open | revived (the only exit) |

`done` is terminal. No state skips — in particular a done-claim can never jump `in-progress →
done` (the review state is mandatory; PROCESS rule).

## Receipts (normative format; duties in Spec A)

Ticket-scoped events (gate passed, review verdict, deviation disposition, acknowledgment) are
appended as lines under `## Receipts`:

`- <UTC ISO-8601> · <event> · <seat> · <verdict-or-action> · <evidence-ref>`

`<evidence-ref>` = commit hash, file path, session id, or review-report pointer. One line per
event; append-only.

## Spawn brief (normative format; sending is Spec B, obeying is Spec A)

Line 1 is exactly `Seat: <seat> · Ticket: T-### · Project: <name>` and nothing else; any
free-form task text begins on line 2 or later (so `<name>` ends at end-of-line).

## Machine-readability contract

- `T-###` token MUST appear in every commit message (free position) and in every Builder
  Report's `**Ticket:**` field. Scripts map commits→tickets syntactically only.
- Greppable anchors, exact: `^Status: ` header · `^\*\*Deviations from spec:\*\*` ·
  `^## Builder Report` · `^## Result` · `^## Receipts` · receipt lines `^- \d{4}-`.
- A non-`none` deviation value is the machine-visible event that triggers deviation routing
  (routing is Spec A's).

## Documentation standard

- Specs: `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`; plans: `docs/superpowers/plans/`
  (existing conventions, unchanged).
- Every project carries `backlog.md`, `tickets/`, `PROCESS.md` (or inherits the methodology
  default), and a STATUS/handover doc for infra state.
- Doc-accuracy ownership and sync-check commissioning are duties: Spec A.
- **Role assignment record:** every project's `PROCESS.md` carries a roles table
  (`| Seat | Held by | ... |`) binding each active seat to its concrete holder (model, session
  type, or human). Binding changes are plan changes (duty: Spec A).

## Migration

The standard applies from adoption. A project adopting it brings existing tickets/backlog into
conformance in the adopting commit — done for this project (T-001..T-005 headers and backlog
normalized alongside this draft).

## Adopted vs adapted from PulseMark

| Item | Decision | Why |
|---|---|---|
| Backlog as lightweight index | adopted | worked in production |
| Ticket spec immutable to builder | adopted | protects the contract |
| Builder Report appended | adopted + numbered re-work reports | done-claim artifact; append-only history |
| Deviation note, implement-best-interpretation | adopted | operator decision (non-blocking) |
| Emoji statuses | replaced with words | greppability |
| Fix-vs-spec judgment | referenced (Spec A) | duty, not format |
| `review` state | added | done-claim review rule is new |
| Machine header, `T-###` token, receipts, spawn brief | added | foreman scripts need syntax |

## Acceptance criteria (T-002) — status

- Normative schema + transition table: above, with grammar conventions. ✔
- One schema for humans and scripts: anchors + authority rules defined. ✔
- Deviation pipeline: format + event defined here; routing in Spec A (explicit cross-refs). ✔
- Adopted/adapted table with reasons. ✔
- Gate: review round 1 applied; targeted re-review + operator approval pending.
