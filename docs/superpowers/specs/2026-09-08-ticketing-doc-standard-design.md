# Spec C — Documentation + Ticketing Standard

> T-002 · Deliverable #1 · Status: draft, pending Codex review + operator approval.
> Boundary: this spec owns artifact formats and lifecycle (the WHAT). Role duties are owned by
> Spec A and referenced only. Derived from the PulseMark contract (verified working baseline:
> `/home/pulsemark/architect-wd/pulsemark/architect/ARCHITECT.md`, `builder/BUILDER.md`).

## Goal

One schema that serves humans and foreman scripts: humans read it naturally; deterministic
scripts parse status, IDs, and receipts without a model. (Concept: "Script vs model" — scripts
operate only on explicit identifiers and recorded evidence.)

## Normative schema

### Ticket file
- Path: `<project>/tickets/T-###-<kebab-slug>.md`. IDs zero-padded three digits, unique per
  project, never reused.
- Line 1: `# T-### — <title>`
- Line 3 (machine-read header, exact format):
  `Status: <status> · Deliverable: <ref> · Owner: <seat>`
- Required sections, in order: `## Spec reference` · `## Scope` · `## Acceptance criteria`
- Appended by builder on completion: `## Builder Report` with labeled lines:
  `**Status:**` `**Branch:**` `**Commit:**` `**Summary:**` `**Deviations from spec:**`
  `**Known issues:**`
- Experiment tickets append `## Result` instead of a Builder Report (same review gate).
- The `## Scope` and `## Acceptance criteria` sections are immutable by the builder seat
  (Spec A defines the seat rule; this spec defines which bytes are protected).

### Backlog index
- Path: `<project>/backlog.md`. One table per phase: `| ID | Title | Deliverable | Status |`.
- Index only — detail lives in ticket files. Status here mirrors the ticket header (the ticket
  file is authoritative on conflict; scripts flag divergence).

### Status lifecycle
`open → in-progress → review → done`, plus `parked` (from any state, back to `open`).

| From | To | Trigger |
|---|---|---|
| open | in-progress | work dispatched/started |
| in-progress | review | done-claim recorded (Report/Result appended) |
| review | done | independent review passed + findings applied + acknowledged |
| review | in-progress | review found material issues |
| any | parked | deliberate deferral, reason recorded in ticket |
| parked | open | revived |

No state skips: a done-claim never jumps to `done` (PROCESS rule: the party that ran the work
never certifies its own conclusions).

### Machine-readability contract
- Ticket ID token `T-###` MUST appear in every commit message and Builder Report for work on that
  ticket — scripts map commits→tickets purely syntactically; semantic "does this work belong to
  its ticket" stays a checker judgment (concept: Script vs model).
- `Status:` header greppable: `^Status: <status> ·` exactly.
- Deviation detection: the literal `**Deviations from spec:**` line; value `none` or prose.
  Any non-`none` value is the event that triggers the deviation pipeline.

## Deviation pipeline (format side)

Builder implements best interpretation, records it under `**Deviations from spec:**` (what, why,
interpretation chosen). The note is the machine-visible event; routing (foreman → checker → SA →
pass or corrective ticket) is Spec A/B territory. A corrective ticket cites the originating
`T-###`.

## Documentation standard

- Specs: `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`; plans: `docs/superpowers/plans/`
  (existing sd-superpowers conventions, unchanged).
- Every project carries: `backlog.md`, `tickets/`, `PROCESS.md` (or inherits the methodology
  default), and a STATUS/handover doc for infra state.
- Doc accuracy is owned by the SA seat; docs are updated to match reality, never the reverse
  (PulseMark rule, adopted verbatim). Sync verification is commissioned independently since the
  SA wrote both (Spec A).

## Adopted vs adapted from PulseMark

| Item | Decision | Why |
|---|---|---|
| Backlog as lightweight index | adopted verbatim | worked in production |
| Ticket spec immutable to builder | adopted verbatim | protects the contract |
| Builder Report appended to ticket | adopted verbatim | the done-claim artifact |
| Deviation note + implement-best-interpretation | adopted verbatim | operator decision (non-blocking flow) |
| Emoji statuses (📋 etc.) | replaced with words | greppability beats glyphs |
| Fix-vs-spec judgment rules | referenced (Spec A, SA seat) | role duty, not format |
| review state | added | done-claim review rule (PROCESS) is new |
| Machine-read header + T-### commit token | added | foreman scripts need syntax, not inference |

## Acceptance criteria (from T-002)

- Normative schema + transition table: above, formats exact.
- Humans + scripts served by one schema: header/token/section contracts defined.
- Deviation pipeline specified at format level.
- Adopted/adapted decisions listed with reasons.
- Gate: Codex adversarial review, then operator approval.
