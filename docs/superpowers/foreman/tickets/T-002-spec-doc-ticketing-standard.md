# T-002 — Spec C: documentation + ticketing standard

Status: open · Deliverable: #1 · Owner: SA

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
