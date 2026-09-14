# sd-superpowers — project pointer

This repo is the superpowers skills fork (skills/ is the source of truth for every consumer —
see RULES.md) plus a nested methodology sub-project. It is not the authoritative current-work
map; **[sd-foreman project map](../sd-foreman/PROJECT.md) is.**

**Una workbench:** shared slug `sd-superpowers` covers BOTH this repo and sd-foreman —
STATUS.md / RULES.md. Read those, not a repo-named page.

## This repo's own sub-project

Foreman methodology delivery lives at
[docs/superpowers/foreman/](docs/superpowers/foreman/) — its own
[backlog](docs/superpowers/foreman/backlog.md),
[PROCESS](docs/superpowers/foreman/PROCESS.md), tickets/, reviews/. This is a **separate
ticket-ID space** from sd-foreman's; it reuses the same numbers. Qualify cross-repo references
explicitly as `sd-superpowers T-0NN`.

## Held — phase-based SA delivery

**Held, not active.** Design/ticketing is written but dispatch does not resume until
sd-foreman's T-016–T-018 startup repairs are verified and the operator authorizes restart —
see [sd-foreman project map](../sd-foreman/PROJECT.md) "Held" section and the
[startup incident & scope-reduction ruling](../sd-foreman/docs/experiments/2026-09-13-startup-failures.md#operator-scope-reduction-2026-09-13).

- [Phase-SA design spec](docs/superpowers/specs/2026-09-13-phase-sa-design.md)
- [Phase-SA implementation/test plan](docs/superpowers/plans/2026-09-13-phase-sa.md)
- [Phase lifecycle](docs/superpowers/specs/2026-09-13-phase-sa/lifecycle.md)
- Tickets: [T-013 baselines](docs/superpowers/foreman/tickets/T-013-phase-sa-baselines.md),
  [T-014 methodology](docs/superpowers/foreman/tickets/T-014-phase-sa-methodology.md),
  [T-015 verification](docs/superpowers/foreman/tickets/T-015-phase-sa-verification.md) (open,
  not dispatchable). Package receipts:
  [T-013 Receipts](docs/superpowers/foreman/tickets/T-013-phase-sa-baselines.md#receipts).

## Skills source

[skills/](skills/) is the actual product. Symlink-only install model — do not install as a
plugin; edits require a Codex review before merge, logged in claude-brain/codex-reviews.md.
(Full install/edit rules: Una workbench `sd-superpowers` RULES.md, not a local file.)

## Other specs/plans

[docs/superpowers/specs/](docs/superpowers/specs/), [docs/superpowers/plans/](docs/superpowers/plans/)
hold both older dated design work and the current phase-sa package, undifferentiated by
folder — filename dates order them. No active/archive split exists yet; do not assume
everything there is live.
