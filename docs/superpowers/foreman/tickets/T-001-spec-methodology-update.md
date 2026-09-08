# T-001 — Spec A: sd-superpowers methodology update

Status: review · Deliverable: #1 · Owner: sa

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
