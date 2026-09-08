# T-001 — Spec A: sd-superpowers methodology update

Status: done · Deliverable: #1 · Owner: sa

## Spec reference
Concept `docs/superpowers/foreman-role-concept.md`: Project structure #1–#2, Roles, Authority,
Trust stance, The reference by phase, Escalation pipeline.

## Scope
Design the changes to sd-superpowers so every session boots knowing the full role cast and its
own seat: role vocabulary (operator, strategic advisor, foreman, SA/architect, builder, checker,
Hermes), the foreman role definition (function: orchestration), which existing skills change and
which gain foreman-awareness (commissioning moves up when a foreman is present; standalone mode
preserved), and where the role canon lives so spawned sessions load it.

## Acceptance criteria
- Spec names every skill/doc file touched and the behavioral change per file, with explicit
  behavior cases for BOTH modes: foreman-present and standalone.
- Foreman-less operation remains fully working (explicit standalone path).
- Role definitions readable by a session at boot (mechanism named).
- Passes Codex adversarial review + operator approval.

## Result

**Spec:** docs/superpowers/specs/2026-09-08-methodology-roles-update-design.md
Draft v2 — Codex review round 1 applied (12 findings), targeted re-review round 2 passed
(finding 7 resolved by this artifact). Awaiting operator approval.

## Receipts
- 2026-09-08T13:33:19Z · codex-review-r1 · checker · 12-findings-reported · task-a5214e962a2783adb
- 2026-09-08T13:33:19Z · codex-review-r2-targeted · checker · 11-of-12-resolved · task-a6a001cc011356ab1
- 2026-09-08T13:33:19Z · operator-review-r3 · operator · 6-findings-reported-and-applied · commit-bc8aecb
- 2026-09-08T13:33:19Z · approval · operator · approved · conversation-2026-09-08
- 2026-09-08T15:55:00Z · operator-amendment · operator · verifier-seat-generalized-and-foreman-write-boundary-sharpened · docs/superpowers/specs/2026-09-08-methodology-roles-update-design.md
- 2026-09-08T16:35:00Z · operator-amendment · operator · verifier-removed-checker-split-into-sa-reviewer-and-code-reviewer-advisor-never-auto-launched · docs/superpowers/specs/2026-09-08-methodology-roles-update-design.md
