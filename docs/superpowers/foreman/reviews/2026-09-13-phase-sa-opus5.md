# Phase-SA package — Claude Opus 5 review

## Commission

- Operator authorized this one SA-commissioned review on 2026-09-13: “Ok, override this rule once. Run claude Opus 5 to review”.
- Requested model: `claude-opus-5`; backend: Claude through Happier daemon, full fresh session.
- Happier CLI 0.2.11; Claude Code 2.1.268.
- Session: `cmtzqktrd0hydpj060liulwp0`; created: 2026-09-13T11:34:15.720000+00:00.
- Read-only review of the design, lifecycle, implementation plan, four tickets and both backlog indices.
- Status: returned, Issues Found for both spec and plan/tickets; no blockers, five important findings.
- Effective model verified from provider message metadata: `claude-opus-5`. Happier reported idle at 2026-09-13T11:37:24.944Z.
- All nine input fingerprints matched after review; the reviewer changed none of the input artifacts.
- One-time override does not amend role-cast or PROCESS.

## Input fingerprints

SHA-256 of the actual working-tree files at dispatch; no input revision has been committed.

- `/home/sd-superpowers/docs/superpowers/specs/2026-09-13-phase-sa-design.md`: `66db789652756523b665a9e769909dc6c00b5ddde39edd2eb60ea172ca93011f`
- `/home/sd-superpowers/docs/superpowers/specs/2026-09-13-phase-sa/lifecycle.md`: `9dfd9ec9a2150baa4bf299429d31ac7c6c2d206d59140d9ff0a8d0b2960608c8`
- `/home/sd-superpowers/docs/superpowers/plans/2026-09-13-phase-sa.md`: `fd124ae1955c925bc30979da542d9d821fa917c000967a7e3d2b5dac44d7f4d7`
- `/home/sd-superpowers/docs/superpowers/foreman/tickets/T-013-phase-sa-baselines.md`: `96a50a5325125a398f82649c788227b025d71a7a689b61b62c99b69859c78a1f`
- `/home/sd-superpowers/docs/superpowers/foreman/tickets/T-015-phase-sa-verification.md`: `aca379109e7f653fcbb19659b8cd07a0eb657e373c15e5eb615091328a77e9b0`
- `/home/sd-superpowers/docs/superpowers/foreman/tickets/T-014-phase-sa-methodology.md`: `4e9b381b1b4639dbf2a0f7f80047e6bc053498f5ab0fcf6b2ac83709fdde0905`
- `/home/sd-foreman/tickets/T-014-persistent-project-sa.md`: `aaf73a77b6d379ef6edd2981777af62df41ec29fdbc9fb47688ff991fa80902b`
- `/home/sd-superpowers/docs/superpowers/foreman/backlog.md`: `64db7501cdfae7d33132675bad41bcceb3ca040fb489721083f37dfde7c0b1e9`
- `/home/sd-foreman/backlog.md`: `bfc2be26166355ed4942c465e764c9b63da2898453b7d234843bf051c6bdb3cb`

## Commissioned brief (verbatim)

```text
Seat: sa-reviewer · Ticket: T-014 · Project: sd-superpowers

Read-only independent review of the current phase-SA design, implementation plan and tickets.
Operator explicitly authorized this one commission by the authoring SA: “Ok, override this
rule once. Run claude Opus 5 to review”. This is a one-time commissioning exception, not a
standing methodology change. You are the independent reviewer, not the implementer or foreman.
Do not edit files, update Una/workbench, spawn other agents, send messages to third parties,
commit, or perform session control. Return your full review in your final response.

User intent from this conversation:
- The operator questioned whether complete SA design up front is realistic and proposed an
  initial thorough multi-file design/spec session followed by a fresh SA per phase for tickets,
  test plan, implementation support and remedial tickets, to manage context and scope creep.
- “I agree there will need to reasonable (re)design stages at the beginning of each phase but
  Ideally the initial SA produces thorough enough documents that i dont need to be involved
  unless significant changes.”
- The operator then instructed: “OK as SA. design and ticket these changes”.

Review these CURRENT WORKING TREE artifacts (new files are not committed):
1. /home/sd-superpowers/docs/superpowers/specs/2026-09-13-phase-sa-design.md
2. /home/sd-superpowers/docs/superpowers/specs/2026-09-13-phase-sa/lifecycle.md
3. /home/sd-superpowers/docs/superpowers/plans/2026-09-13-phase-sa.md
4. /home/sd-superpowers/docs/superpowers/foreman/tickets/T-013-phase-sa-baselines.md
5. /home/sd-superpowers/docs/superpowers/foreman/tickets/T-014-phase-sa-methodology.md
6. /home/sd-superpowers/docs/superpowers/foreman/tickets/T-015-phase-sa-verification.md
7. /home/sd-foreman/tickets/T-014-persistent-project-sa.md
8. Both associated backlog.md indices.

Authoritative current repository context to inspect as needed:
- /home/sd-superpowers/skills/{role-cast,ticket-discipline,brainstorming,writing-plans,subagent-driven-development}/
- /home/sd-superpowers/docs/superpowers/foreman/PROCESS.md
- /home/sd-superpowers/docs/superpowers/specs/2026-09-08-methodology-roles-update-design.md
- /home/sd-superpowers/docs/superpowers/specs/2026-09-08-orchestration-harness-design.md
- /home/sd-superpowers/docs/superpowers/specs/2026-09-08-ticketing-doc-standard-design.md
- /home/sd-foreman/{PROCESS.md,foreman/FOREMAN.md,docs/STATUS.md,docs/runbook.md}

Standing written-spec and plan review rubrics:
/home/sd-superpowers/skills/brainstorming/spec-document-reviewer-prompt.md
/home/sd-superpowers/skills/writing-plans/plan-document-reviewer-prompt.md

Central question: Could another competent agent implement this specification and ticket set
without material assumptions, while satisfying the operator's stated intent?

Check grounding, completeness, coherence, authority boundaries, unsupported technical claims,
missing acceptance/tests, dependencies, actionable task decomposition, edge cases, unnecessary
complexity and materially simpler complete alternatives. Check plan/ticket alignment to the
specification. Do not demand implementation evidence already explicitly scheduled for the build
as a prerequisite to writing this design; distinguish unverified design claims from planned tests.

For each BLOCKER or IMPORTANT finding cite exact file/section or line evidence, the confirmed
requirement affected, impact and smallest valid correction. Evaluate remedies subtractive-first:
Delete, Narrow, Simplify, Reuse, Clarify, then Add only if required. For an additive correction
state the subtractive alternative considered and why the addition is necessary. Do not redesign
for taste or add speculative features. Label material factual claims VERIFIED, INFERRED or
UNSUPPORTED when relevant. Also classify required rework minor/major for existing routing.

Return:
- Spec verdict: Approved | Issues Found
- Plan/ticket verdict: Approved | Issues Found
- BLOCKERS, IMPORTANT, OPTIONAL (write None when empty)
- Files inspected; checks actually performed; limitations/unverified assumptions.
Do not certify deployment or change ticket statuses. You may approve the design for the next
gate; operator approval remains separate.
```

## Reviewer response (verbatim)

# Review: phase-based SA package (spec, plan and tickets)

**Spec verdict: Issues Found.** **Plan/ticket verdict: Issues Found.**
The design fits what you asked for: a thorough first design, then a fresh SA per phase, and the operator only for big changes. No blockers. Five important gaps would force a builder to guess. All five are small fixes to SA documents (minor rework).

## BLOCKERS
None.

## IMPORTANT

**I-1. The "project has adopted this" switch is never defined.**
- **Evidence:** spec R3 (lines 114–116) requires "an explicit PROCESS.md adoption reference". sd-superpowers T-014's acceptance says "Adoption is conditional; live symlink consumers do not receive enabled partial adoption." Nothing says what that reference looks like or which PROCESS.md counts. sd-foreman's PROCESS.md only inherits the default (line 3).
- **Also missing:** `docs/superpowers/foreman/PROJECT-SETUP.md:75-77` still says "The sa is ONE persistent session … exempt from per-task teardown." That file is in neither T-014's file list.
- **VERIFIED:** the live skills are symlinks into `/home/sd-superpowers/skills`, so any skill change that lands is live immediately.
- **Requirement hit:** R3 (non-adopting projects keep today's workflow). Every skill check that asks "has this project adopted?" depends on this.
- **Impact:** the builder must invent the marker. The skills, FOREMAN.md and PROCESS.md could each invent a different one. New projects get set up with the old rule.
- **Fix (Clarify):**
  - Name one exact marker in R3 (a fixed line or heading in the project's PROCESS.md that points to the approval receipt). All skills check only that.
  - Add PROJECT-SETUP.md to sd-superpowers T-014's files.
  - Say who merges the isolated checkout into the live tree, and when.
- **Rework:** minor.

**I-2. The sd-superpowers tickets rely on a foreman that doesn't supervise that project, and approval receipts have nowhere to go.**
- **Evidence:** T-013, T-014 and T-015 all say "Foreman commissions" and require "foreman acknowledgment". The spec (line 129) says reviews are "commissioned by the foreman under current rules".
- **What the records show:**
  - sd-superpowers PROCESS.md still has its early-stage roles table (Owner acknowledges; lines 8–22).
  - Its last tickets were "operator acknowledged" (commit a18fa2c).
  - This review was commissioned by the authoring SA, under a one-time operator override.
  - The foreman that exists runs sd-foreman (STATUS lines 19–23).
  - INFERRED: no foreman currently supervises sd-superpowers.
- **No receipt home:** sd-superpowers T-014's gate needs "the package's review/approval receipts recorded". No ticket or file is named to hold them, and there is no spec ticket for this package.
- **Two tickets share one evidence file:** sd-foreman T-014 creates `docs/experiments/2026-09-13-phase-sa-lifecycle.md`, and sd-superpowers T-015 writes runtime evidence to the same path. Neither says whether to append to it or replace it.
- **Requirement hit:** R3 gates, and role-cast's rule that the foreman acknowledges when one supervises, the operator otherwise.
- **Impact:** the gates can't be met or checked as written, so dispatch stalls or someone improvises.
- **Fix (Clarify + Reuse):**
  - State who dispatches and acknowledges each sd-superpowers ticket. Default: the operator, unless a foreman is bound in PROCESS.md.
  - Put the package's review and approval receipts under an existing ticket, e.g. T-013 `## Receipts`.
  - Say T-015 appends a new section to the shared evidence file.
- **Rework:** minor.

**I-3. The sd-foreman T-014 rewrite parks an operator ruling behind an unapproved package.**
- **Evidence:** the committed ticket (79c08f4) put operator ruling A into effect: spawn the SA once, reuse it, respawn if it dies. The working-tree version replaces that scope in place. It adds the gate "Do not dispatch the old project-lifetime behavior from this ticket" and makes it depend on the whole package plus sd-superpowers T-013 and T-014.
- **Requirement hit:** role-cast says plan/scope approval belongs to the operator (`skills/role-cast/SKILL.md:33`). INFERRED: "design and ticket these changes" authorizes new tickets. It doesn't clearly authorize stalling an open ruling that can be done now.
- **Impact:**
  - If the package is delayed or rejected, ruling A's remaining work is blocked.
  - The ticket's file name (`persistent-project-sa`) no longer matches its content.
  - No record of the replaced scope exists outside git history.
- **Fix (Delete/Narrow):** restore sd-foreman T-014 to ruling A. Put phase replacement in a new sd-foreman ticket that depends on it. Otherwise, get an explicit operator ruling that ruling A is replaced, and record it as a receipt.
- **Rework:** minor. The decision belongs to the operator.

**I-4. Nothing tests whether a reviewer catches an SA calling a big change "routine".**
- **Evidence:** R2 (line 106) has the SA decide whether a change is significant, and the foreman only checks that evidence exists. The actor table (line 51) makes reviewers the check that "detect[s] unauthorized decisions". But:
  - S3 only tests an SA that raises the change honestly.
  - S4 tests reviewers on phase coverage, not on significance.
  - sd-superpowers T-014's acceptance changes the reviewer prompts but has no criterion for this check.
- **Requirement hit:** R2, where independent review replaces operator approval of each phase spec.
- **Impact:** the one safeguard that replaces you is written into prose but never tested. An SA that under-labels changes would pass T-015.
- **Fix (Add, narrowly):** add an S3 variant where the SA's artifact labels the cumulative sync change as routine and the reviewer must flag it. Add a matching line to T-014's acceptance.
- **Why add:** the subtractive option, trusting the SA's own label, contradicts R2's own independence rule.
- **Rework:** minor.

**I-5. The spec describes the wrong failure for the done-ticket case.**
- **Evidence:** lifecycle line 99 and S8 warn of "erroneous orphan teardown" when the standing SA's original ticket is done. Current boot (`FOREMAN.md:9-13`) only reads receipts on tickets that aren't done. A live SA from a done ticket therefore matches no receipt, so it's treated as an **unknown session**: escalate, touch nothing. It is not torn down.
- **Impact:** builders will test for a teardown that current rules don't do, and miss the real problem: a false escalation to you on every restart.
- **Fix (Clarify):** reword lifecycle line 99, S8 and sd-foreman T-014's acceptance to "not treated as unknown/orphan (no escalation, no teardown)".
- **Rework:** minor.

## OPTIONAL
- **Simpler handover (my judgment):** R7 checks handover sufficiency twice: an independent review before stopping the old SA, then the new SA's readiness check. The readiness check is the real cold-start test. Consider dropping the pre-stop review, or having the new SA do a read-only readiness check before the old one stops (only one SA writes at a time). That saves one review session per phase.
- **sd-superpowers T-014 is large:** about 10 skill files, 3 specs, the concept doc and 7 probe scenarios in one builder session. Plan A split the same kind of work across T-007..T-011. Splitting would fit the package's own context goal.
- **Weak count:** T-013 says "Multiple fresh sessions" with no minimum. Name one (e.g. at least 2 per scenario, or reuse Plan A's count).

## Claim labels
- VERIFIED: brainstorming allows separate sub-project cycles (`SKILL.md:120`).
- VERIFIED: writing-plans adds a task for any spec requirement with no task (`SKILL.md:175`).
- VERIFIED: FOREMAN.md exempts the SA from teardown (lines 49, 88).
- VERIFIED: the committed sd-foreman T-014 was one persistent SA.
- VERIFIED: all files in the plan's T-014 list exist.
- UNSUPPORTED (flagged by the package itself, fine): the runtime replacement sequence works. S7–S9 are scheduled to test it.

## Files inspected
- Every file in the brief: spec, lifecycle, plan, both T-014s, T-013, T-015, both backlogs (with `git diff` against HEAD).
- Both review rubrics.
- Skills: ticket-discipline and role-cast in full; brainstorming, writing-plans and subagent-driven-development by grep.
- sd-superpowers: PROCESS.md, PROJECT-SETUP.md, and T-012's receipts.
- sd-foreman: PROCESS.md, FOREMAN.md (full), STATUS.md, runbook.md, and the git history of T-014.
- Spec A, B and the concept doc by grep.

## Checks performed
- Traced each cited repository claim to file and line.
- Diffed both uncommitted sd-foreman edits against HEAD.
- Checked the skill symlinks and that the listed target files exist.
- Mapped spec requirements to plan tasks and tickets (R1–R3 to sd-superpowers T-014; R6–R7 to both T-014s; S1–S10 to T-013 and T-015).
- Checked ticket format against ticket-discipline: header lines, section order and ID uniqueness pass.

## Limits
- Spec C, runbook.md and subagent-driven-development were read by grep only, not in full.
- No live Happier commands were run.
- Whether any foreman supervises sd-superpowers is inferred from records, not confirmed.
- I can't see the operator conversation beyond the quotes in the brief.
- No ticket statuses changed. Nothing is certified for deployment.

## SA verification and disposition (review-only turn)

Review request completed; this is a findings assessment, not application of proposed corrections.
No independent approval or behavioral verification is claimed.

| Finding | Ground-truth check | Disposition |
|---|---|---|
| I-1 | R3 does not define adoption-marker syntax or which project PROCESS is authoritative; plan omits PROJECT-SETUP.md, whose lines 75–77 encode standing project SA; local role-cast symlinks resolve into the source repo | Confirmed. Recommend exact project-local marker/receipt reference, setup-document inclusion and named integration owner/gate |
| I-2 | sd-superpowers PROCESS lines 8–22 retain bootstrap ownership while sd-foreman STATUS records its own M1 handover; new tickets assume a foreman; no package receipt home or shared-evidence append contract is specified | Confirmed documentation/authority gap. Actual absence of an sd-superpowers foreman remains INFERRED, not proved. Recommend explicit per-project commissioning/acknowledgment and one receipt home; preserve prior runtime evidence by append |
| I-3 | Compared committed and working-tree sd-foreman T-014: previous operator ruling is replaced and the draft bars dispatch of the old behavior | Confirmed scope replacement; whether the earlier design-and-ticket instruction authorized it is an interpretation, not a factual finding. Recommend restoring the approved ticket and putting rotation in a separate successor, unless the operator explicitly wants replacement. No restoration or new ticket applied during this review-only turn |
| I-4 | S3 tests producer escalation; no fixture supplies a falsely routine-labelled significant change to the independent reviewer | Confirmed. Recommend an adversarial reviewer variant and corresponding ticket acceptance |
| I-5 | FOREMAN.md lines 9–17 distinguish unmatched unknown sessions (escalate/touch nothing) from matched orphans (stop); done-ticket receipts are omitted by current scan | Confirmed. Recommend naming both false unknown-session escalation and improper teardown in the new contract/test |

Optional suggestions are not required corrections: retain a reviewed checkpoint plus successor
readiness for now (different checks); consider splitting methodology work if dispatch context
requires it; a fixed repeat count is not necessary to report honest independent-session evidence.
The existing requirement to use multiple sessions remains.

No design, plan, ticket scope, runtime procedure or role binding was changed by this review.
Only review-status links and this evidence record are updated.

## Teardown receipt

Happier session stop returned stopped:true. A subsequent session-list query verified
`cmtzqktrd0hydpj060liulwp0` has `active:false` (2026-09-13; this session only).

## Applied corrections — 2026-09-13

Operator instruction: “Apply the recommended corrections”. This authorizes correcting the
proposal and restoring the original ticket; it is not written-spec approval or authorization
to execute the implementation tickets. The verbatim review above is preserved unchanged.

| Finding | Applied correction | Authoritative locations |
|---|---|---|
| I-1 | Exact project-root adoption marker and approval lookup; operator-assigned integration after joint verification; PROJECT-SETUP.md included in implementation scope; invalid marker cases in S10 | Spec R3; plan file map/integration steps/S10; sd-superpowers T-014 |
| I-2 | Per-project commissioning/dispatch/acknowledgment table with operator default for sd-superpowers until explicit assignment; package receipts under existing sd-superpowers T-013; runtime evidence append ownership defined | Plan Authority and evidence; all package tickets; both indices |
| I-3 | Restored sd-foreman T-014 byte-for-byte from HEAD; created sd-foreman T-015 as successor dependent on original T-014 and methodology work; original remains independent of new-package gates | sd-foreman tickets/T-014-persistent-project-sa.md and tickets/T-015-phase-sa-lifecycle.md; plan and indices |
| I-4 | Independent reviewer must reject falsely routine-labelled significant/cumulative changes; acceptance and S3 variant test the reviewer, not only the producer | Spec R2; plan S3; sd-superpowers T-014/T-015 |
| I-5 | Distinguish unknown-session escalation from attributable-orphan teardown; valid standing SA with done spawning ticket must trigger neither false escalation nor teardown | Lifecycle R7; plan S8; runtime T-015 and integrated verification ticket |

The optional redesigns were not adopted. No skill, runtime procedure, setup default or model
binding was changed: those implementation changes remain ticketed. No new reviewer commission
was made; the one-time commissioning exception has not been extended. Local verification is
recorded below; Opus has not independently re-reviewed the corrected revision.

### Local verification of applied corrections

- Original sd-foreman T-014 matches its committed HEAD bytes exactly; its backlog title/status
  also match the original. New phase work is sd-foreman T-015, with the original as prerequisite.
- Four package implementation tickets have valid headers, required section order, unique IDs
  within their repositories and matching open-status backlog rows.
- Local design/plan links and package receipt evidence paths resolve; no placeholders, conflict
  markers or trailing whitespace found. Both repositories pass git diff --check.
- Checked every I-1–I-5 correction across the spec, plan, acceptance criteria and dependencies;
  preserved the complete verbatim Opus response. No behavioral tests run or fresh review claimed.
