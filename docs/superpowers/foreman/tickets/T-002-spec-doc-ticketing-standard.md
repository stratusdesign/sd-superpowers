# T-002 — Spec C: documentation + ticketing standard

Status: done · Deliverable: #1 · Owner: sa

## Spec reference
Concept: "Ticketing and documentation (inherited, working pattern)"; PulseMark
`architect/ARCHITECT.md` + `builder/BUILDER.md` (verified working baseline).

## Scope
The shared schema both repos use: backlog index format, ticket file format (status, spec,
acceptance criteria, Builder Report, deviation notes), status lifecycle, doc-accuracy ownership,
and what makes tickets machine-readable by the foreman (explicit IDs on commits/reports so
scripts stay mechanical — concept, Script vs model).

## Acceptance criteria
- Normative schema (field list, formats) + a status transition table — not prose description.
- One schema serves humans and foreman scripts (IDs, statuses parseable).
- Deviation-note pipeline specified (builder implements best interpretation + notes → checker pass → SA).
- Adopted-verbatim vs adapted-from-PulseMark decisions listed with reasons.
- Passes Codex review + operator approval.

## Result

**Spec:** docs/superpowers/specs/2026-09-08-ticketing-doc-standard-design.md
Draft v2 — Codex review round 1 applied (12 findings), targeted re-review round 2 passed
(finding 7 resolved by this artifact). Awaiting operator approval.

## Receipts
- 2026-09-08T13:33:19Z · codex-review-r1 · checker · 12-findings-reported · task-a5214e962a2783adb
- 2026-09-08T13:33:19Z · codex-review-r2-targeted · checker · 11-of-12-resolved · task-a6a001cc011356ab1
- 2026-09-08T13:33:19Z · operator-review-r3 · operator · 6-findings-reported-and-applied · commit-bc8aecb
- 2026-09-08T13:33:19Z · approval · operator · approved · conversation-2026-09-08
