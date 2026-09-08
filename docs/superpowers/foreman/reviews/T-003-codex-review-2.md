# T-003 / Spec B — Codex targeted re-review (round 2)

> 2026-09-08 · fresh-context Codex · scope: adequacy of the 10 round-1 resolutions in draft v2
> + regressions · verbatim. Disposition (sa): finding 5 completed and N2 applied in draft v3;
> finding 8 restructured as a submitted Spec C amendment; N1 raised to operator (open item 3).

1. Check moment 3 needed a real event source or an explicit observability limitation

Verdict: resolved

Exact v2 passages:

- Goal, lines 12–16: "watches the four check moments through their v1-observable event sources — each moment's observability limit stated in the matrix, not glossed"
- Matrix #3, line 91: "Source = the raised event only" and "Unraised instances are unobservable pre-action in v1"
- V1 boundaries, lines 210–213: "v1 cannot pre-empt an unraised irreversible action"

This adequately implements the requested narrowing. The source is precisely identified as the worker's raise message, and unraised instances are explicitly excluded from the observable set. The Goal no longer claims observation without qualification.

2. Worker-blocking and fail-closed claims needed to distinguish gate blocking from execution stopping

Verdict: resolved

Exact v2 passages:

- Matrix #3, line 91: "gate progression freezes … No mechanical worker stop is claimed; stop candidate = session stop (E8), not relied on"
- Escalation semantics, lines 106–111: "gate-blocking, not execution-stopping" and "It does not mechanically stop a running bypass worker"
- Failure behavior, lines 117–122: "ticket/gate progression stops" while "A live bypass worker mid-task is NOT stopped by foreman death"
- Acceptance status, lines 224–225: fail-closed is "scoped to what the machinery actually enforces (gates, not worker execution)"

These passages make the requested distinction consistently inside Spec B. Session stopping is correctly treated as an E8 candidate rather than an existing guarantee.

There is, however, a newly exposed conflict with the governing ticket's unchanged Scope; see New finding N1 below.

3. Hermes→Telegram fallback needed to remain manual until a foreman→Hermes channel exists

Verdict: resolved

Exact v2 passage, lines 100–105:

> "Fallback: Hermes → Telegram — operator/Hermes-initiated until E6."
> "the foreman cannot invoke it today"
> "Automatic fallback dispatch exists only after E6 passes."

The available current trigger and the missing upstream channel are both stated explicitly. The corresponding experiment is also correctly named at line 193.

4. Check moment 1 needed either a work-start event or a narrower identity-only claim

Verdict: resolved

Exact v2 passage, line 89:

> "Observes session identity only — it catches unknown/unregistered sessions. A known session doing unrelated work inside itself is NOT observable here; … The concept's work-start/heartbeat contract is deliberately not built in v1 — deferred to M1"

This is the narrower resolution requested in round 1. The event wording was also narrowed to "A session doing work not tied to a valid assigned ticket in `in-progress`."

The claim that this limitation "leaks to" other controls is separately overbroad; see N2.

5. The artifact-diff gate needed a deterministic baseline, coverage, authorization association, and failure result

Verdict: partial

Exact v2 passage, line 90:

> "baseline = the commit hash recorded in the most recent acknowledgment receipt"
> "sweep = `git diff <baseline>..HEAD` over spec files (entire) and ticket `## Scope` / `## Acceptance criteria` sections + `Status:` headers"
> "every hunk touching a protected range must match a raise receipt referencing it; unmatched hunk = breach flag"
> "Diff sweep fails → no acknowledgment can be issued"

This supplies a committed baseline, protected ranges, a nominal authorization association, and a failure result. Two important pieces remain incomplete:

- `git diff <baseline>..HEAD` does not inspect uncommitted index/worktree changes, so a silent protected edit can exist at sweep time without appearing in the specified comparison.
- "match a raise receipt referencing it" is not mechanically defined. Spec C lines 63–71 permits an `<evidence-ref>` consisting of a commit hash, file path, session ID, or review-report pointer, but defines no hunk/range identifier or matching rule. Therefore deterministic authorization-to-hunk association is still underspecified.

6. Load-bearing Happier actions needed explicit live experiments before acceptance could be claimed

Verdict: resolved

Exact v2 passages:

- Architecture, lines 30–34: "Live semantics of every load-bearing action — spawn, message, status/wait, stop/teardown — are E8 (shakedown), not assumed."
- Teardown, lines 66–67: existence is source/docs-verified while "live semantics E8"
- E8, line 195: explicitly covers `session_spawn_new`, `session_message_send`, status/wait, and stop/teardown end-to-end.
- Acceptance status, lines 221–222: unexercised actions are explicitly routed to E8.

The draft no longer silently treats catalogue presence as proven live behavior.

7. Hermes spawning the foreman needed a weaker evidence label and an end-to-end experiment

Verdict: resolved

Exact v2 passages:

- Architecture, lines 25–29: verification is limited to authentication, registration, and visibility; "an end-to-end Hermes-triggered spawn is E9. Until E9: the operator spawns the foreman by hand."
- Hermes integration, lines 153–155: repeats that end-to-end spawning is E9 and remains manual until then.
- E9, line 196: "Hermes-triggered foreman spawn, end-to-end on the host daemon."

This now matches the receipt's actual evidentiary strength.

8. The journal schema had to move to Spec C or the specifications' ownership boundary had to be consistently changed

Verdict: unresolved

Exact v2 passages:

- Boundary, lines 5–8: "artifact formats are Spec C's (one flagged exception: §Foreman journal)"
- Journal section, lines 132–140: Spec B still normatively defines `foreman/journal.md` as append-only, lists its event types, and mandates "Spec C receipt-grammar lines verbatim"
- Lines 136–140 then acknowledge the conflict and defer the choice: "Operator picks at approval: (a) accept this narrow exception as-is, or (b) amend approved Spec C"
- Open items, lines 198–201: journal ownership remains an operator decision.

Flagging the contradiction is not the requested resolution. Neither option has been selected, Spec C has not been amended, and the Specs' ownership boundary has not been consistently narrowed. Spec B therefore still defines an artifact format while stating that artifact formats belong to Spec C.

9. "Fresh-spawn is always safe" needed qualification and explicit preconditions

Verdict: resolved

Exact v2 passage, lines 74–78:

> "fresh-spawn-from-docs is the default handoff and default recovery path — safe under its preconditions"
> "authoritative artifacts current and committed; bootstrap/brief applied … any in-flight uncommitted worker state deliberately discarded"

The universal claim is removed and replaced with a conditional recovery rule whose preconditions are explicit.

10. Check moment 1 needed to use the authoritative `in-progress` lifecycle state rather than "open ticket"

Verdict: resolved

Exact v2 passage, line 89:

> "A session doing work not tied to a valid assigned ticket in `in-progress`"

This now agrees with Spec C's dispatch transition at Spec C lines 51–55.

New findings introduced by v2

N1 — Material: v2's gate-only resolution now contradicts T-003's unchanged approved Scope

Spec B lines 106–110 and 117–122 explicitly state that escalation does not mechanically stop a worker and guarantees only gate progression. But T-003 line 13 still scopes escalation as "worker blocks."

Because v2 marks the T-003 fail-closed criterion complete at lines 224–225 while deliberately declining the ticket's stated behavior, the authoritative ticket and its deliverable now disagree. The ticket scope needs an operator-approved amendment, or the spec needs a tested mechanical stop.

N2 — Minor: the new check-moment-1 fallback wording overstates what the other controls necessarily observe

Matrix #1, line 89 says unrelated work in a known session "leaks to #2's diff gate, #4's envelope, and the completion checker." But:

- #2, line 90, examines only protected spec/ticket ranges.
- #4, line 92, observes effort consumption, not semantic work identity.
- The completion checker runs only after a done-claim, lines 63–65.

Known-session unrelated work that neither changes protected ranges nor exceeds its envelope—and that produces no done-claim—need not reach any of those controls. "May later be detected by" would be accurate; "leaks to" implies routing or coverage the mechanisms do not guarantee.

Overall: 8 resolved, 1 partial, 1 unresolved. The blocker resolutions are adequate, but finding 8 cannot be counted as applied, and the v2 diff-gate construction still needs a complete working-tree comparison and a machine-readable raise-to-hunk association rule.
