# Final Whole-Branch Review and Finish

The final whole-branch review gets a package too: run
`scripts/review-package PLAN_FILE MERGE_BASE HEAD` (MERGE_BASE = the commit the
branch started from, e.g. `git merge-base main HEAD`) and include the printed
path in the final review dispatch, so the final reviewer reads one file instead
of re-deriving the branch diff with git commands. Dispatch on the most capable
available model (see `model-selection.md`), using
superpowers:requesting-code-review's
[code-reviewer.md](../../requesting-code-review/code-reviewer.md). Point it at
the ledger's deferred-minor and parked lines so it can triage which must be
fixed before merge.

This broad final review is a fresh, most-capable independent review. The
controller selects its route per superpowers:requesting-code-review's
role-based contract; for this whole-branch gate, escalate to the cross-family
route only when the change is risky or consequential enough to justify it,
never merely for symmetry. Tasks whose author already received an independent
cross-family review at their task gate do not need it repeated here for that
reason alone. Do not add a second whole-branch reviewer.

## Findings

If the final review returns findings, first verify and adjudicate them against
the requirements and evidence — apply evidence-backed findings and reject unsupported,
invented, or preference-only ones with reasoning — then dispatch ONE fix
subagent with the complete accepted-findings list — not one fixer per finding.
Per-finding fixers each rebuild context and re-run suites; a real session's
final-review fix wave cost more than all its tasks combined.

Then run exactly one scoped re-review of the fix wave
(`scripts/review-package PLAN_FILE FIX_BASE HEAD` over the fix range,
[../re-review-prompt.md](../re-review-prompt.md)). Select this re-reviewer from
the fix author's type using Independent review routing (`model-selection.md`);
there is no task-reviewer thread to inherit. Dispatch a fresh reviewer with the
complete scoped prompt. A same-family (`general-purpose`) reviewer gets an
explicit model tier; a cross-family reviewer is read-only (see
`codex-route.md`).

Adjudicate any residual findings as in the task loop's breaker: park with
rulings, or stop on load-bearing ones. There is no second fix wave — residual
load-bearing findings surface to your human partner when
finishing-a-development-branch presents the options.

## Finish

When the final whole-branch review is clean and its fixes are merged, delete
this plan's workspace (`rm -rf <workspace>`) — the git history is the record
now. Sibling directories belong to other plans; leave them alone.

Use superpowers:finishing-a-development-branch.
