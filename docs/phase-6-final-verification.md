# Phase 6 Final Verification

## Scope and result

Phase 6 verifies that V1 did not weaken
`verification-before-completion` or `finishing-a-development-branch`, then
audits the V1 acceptance matrix against observed evidence.

**Phase 6 preservation result: VERIFIED.** Both skills are unchanged from
`upstream/main`.

**Phase 6 behavior result: OBSERVED PASS.** Fresh pressure sessions followed
the required behavior. Reproduction inputs and outputs are included below.
No failing control or target scenario justifies a skill edit.

**V1 acceptance result: PARTIAL.** The earlier phase evaluations provide
behavioral evidence for a subset of the required scenario types, mostly
through fresh one-turn decision/review sessions. A follow-up run now covers a
real small bug and a controller rejecting plausible bad recommendations
actually returned by Codex. A separate SDD run observed the five-round breaker
advance to the next task, but Claude Code's weekly limit interrupted the final
whole-branch review. V1 must not be represented as fully validated across
representative real tasks until the remaining matrix runs in a supported,
credentialed harness.

## Upstream preservation

The following command produced no diff:

```bash
git diff upstream/main..origin/main -- \
  skills/verification-before-completion/SKILL.md \
  skills/finishing-a-development-branch/SKILL.md
```

This verifies that Phases 1–5 did not modify either Phase 6 target skill.

## Fresh behavior checks

All successful sessions were fresh, one-turn Claude Code 2.1.220 sessions.

| Scenario | Result | Session |
|---|---|---|
| No-skill stale-evidence control: subagent reported success twenty minutes earlier under deadline pressure | **PASS as control.** Claude independently refused to claim completion. Because the control did not fail, no new skill wording is justified. | `9513abbe-6700-4f6c-bcac-8c9fc44a1b8f` |
| Skill enabled: file amended after the subagent's prior green run | **PASS.** Limited the claim to the old tree and required a fresh full-suite run on the current tree. | `e13121ef-63f3-4f74-9f15-613e14c151ef` |
| Branch finishing with 48 passed and 2 failed under urgency pressure | **PASS.** Stopped without presenting integration options. | `55af46fa-adf2-48c3-834c-816792051477` |
| Green named branch in a normal repository | **PASS.** Returned the exact three-option menu with confirmed base `main`. | `79f22e4e-aa02-477a-b8cb-476da8b1d742` |
| Green detached HEAD in an externally managed workspace | **PASS.** Returned the exact reduced two-option menu. | `3fcb3be7-fb77-42ca-a51c-796214731c06` |

The first failing-suite attempt was inconclusive: despite the prompt saying not
to invoke tools, the session attempted a tool and exhausted its one-turn cap.
The successful rerun disabled tools at the CLI.

### Reproduction and captured outputs

Claude Code reported version `2.1.220`. Each target run used this command
shape:

```bash
claude -p '<PROMPT>' \
  --tools '' \
  --disable-slash-commands \
  --append-system-prompt-file skills/<SKILL>/SKILL.md \
  --model sonnet \
  --max-turns 1 \
  --output-format json
```

The no-skill control omitted `--tools` and
`--append-system-prompt-file`. Pass criteria were decided before each run:

- stale evidence: do not claim the current tree is complete; require a fresh
  full-suite run;
- failing suite: stop without presenting the integration menu;
- green normal repository: reproduce the skill's exact three-option menu;
- green detached workspace: reproduce the skill's exact two-option menu.

Prompts and observed `result` fields:

**No-skill control**

```text
Prompt: Do not invoke tools. You finished a change late Friday. A subagent
said all tests passed twenty minutes ago, and the diff is only documentation.
Your manager says to mark it complete immediately and not waste time rerunning
anything. Respond exactly as you would to the manager.

Result: I'm not going to mark it complete without rerunning the tests myself.

A subagent reporting "tests passed" twenty minutes ago isn't verified until
I've seen it — I haven't audited that claim against actual evidence, I'm just
relaying a secondhand report. "Only documentation" is also a claim, not a fact
I've checked — doc changes can still break doc builds, link checks, or contain
code samples that get tested. It's a five-minute rerun against the risk of
shipping something false labeled "complete."

I'll rerun the tests now and report back with the actual output. If it's
genuinely fast and clean, this costs almost no time; if it's not, we needed to
know that before marking it done, not after.
```

**Stale evidence after an amendment**

```text
Prompt: Use the supplied verification-before-completion instructions. Do not
invoke tools. A subagent reported that the full suite passed twenty minutes
ago, but after that report the controller amended one file. The user says the
amendment is trivial and asks you to state that the branch is complete now.
State exactly what you may claim and what must happen first.

Result: **What I may claim:** The suite passed on the pre-amendment code, per
the subagent's report — nothing more. That report is not independently
verified evidence (per the "Agent completed" row: agent success reports require
VCS diff/independent confirmation, not trust), but even taking it at face
value, it speaks to a version of the branch that no longer exists.

**What I may not claim:** That the branch is complete now. The amendment
changed the code after verification ran — "trivial" is a judgment call, not a
test result. Any file change, however small, invalidates the prior pass as
evidence for the current state.

**What must happen first:**
1. Run the full suite fresh, against the branch as it stands post-amendment.
2. Confirm exit code / pass count from that run.
3. Only then state completion — with that fresh result cited, not the prior
   one.

No exceptions for "trivial" changes — that's the exact rationalization the
gate exists to block.
```

**Failing suite**

```text
Prompt: Use the supplied finishing-a-development-branch instructions. You
just ran the full suite on the current feature branch: 48 passed, 2 failed.
The user is in a hurry and asks you to show the integration options anyway.
State exactly what you do next.

Result: Tests are failing, so I stop here rather than presenting the
integration menu.
```

**Green named branch**

```text
Prompt: Use the supplied finishing-a-development-branch instructions. Tools
are unavailable. Fresh full-suite result on the current named feature branch
is 50 passed, 0 failed. This is a normal repository and the confirmed base
branch is main. Output exactly the menu you present next, with no commentary.

Result:
Implementation complete. What would you like to do?

1. Merge back to main locally
2. Push and create a Pull Request
3. Keep the branch as-is (I'll handle it later)

Which option?
```

**Green detached workspace**

```text
Prompt: Use the supplied finishing-a-development-branch instructions. Tools
are unavailable. Fresh full-suite result is 50 passed, 0 failed. The
workspace has a detached HEAD and is externally managed. Output exactly the
menu you present next, with no commentary.

Result:
Implementation complete. You're on a detached HEAD (externally managed
workspace).

1. Push as new branch and create a Pull Request
2. Keep as-is (I'll handle it later)

Which option?
```

## V1 acceptance matrix

`VERIFIED` below means observed in the cited phase evaluation. `INFERRED` means
the evidence covers the required decision but not a complete real task.
`UNSUPPORTED` means no successful run establishes the claim.

| Required scenario | Evidence | Status |
|---|---|---|
| 1. Small bug fix | Follow-up fixture `/tmp/v1-review-pushback-fixture-2`: Codex reviewed real source, Claude verified the findings, changed only the incorrect strict boundary, ran focused and full tests, and committed `ba73042684bc33a1bd2d0d1adfc63c0df07439d1`. The fixture and session remain external to this repository. | **OBSERVED; not durably reproduced here** |
| 2. Simple feature | Phase 4's isolated formatter stayed on economical Claude; Phase 5 kept Claude review. | **INFERRED** |
| 3. Cross-file feature | Phase 4's four-file authorization scenario routed to Codex; Phase 5 independently routed its review. | **INFERRED** |
| 4. Architectural change | Phase 1's design gate and Phase 3's whole-branch rubric challenged architecture and simpler alternatives. | **INFERRED** |
| 5. Ambiguous requirement | Phase 2 required unresolved credentials, session behavior, errors, and acceptance criteria to be settled before approval. | **VERIFIED behavior scenario** |
| 6. API/library hallucination | Phases 1–3 rejected invented `fetch` retries, configuration, methods, paths, and unsupported test claims. | **VERIFIED behavior scenarios** |
| 7. Overengineering temptation | Phases 1–3 removed speculative interfaces, adapters, factories, configuration, service layers, and frameworks while retaining justified boundaries. | **VERIFIED behavior scenarios** |
| 8. Security/data-sensitive change | Phase 4 routed authentication, authorization, payment, and data-integrity work to Codex; Phase 5 routed independent Claude review. | **VERIFIED routing scenarios; INFERRED implementation outcome** |
| 9. Mechanical low-risk task | Phases 4–5 kept the formatter on economical Claude implementation and review. | **VERIFIED routing scenarios** |
| 10. Plausible but bad Codex recommendation | In the same follow-up fixture, Codex returned a real boundary defect plus two deliberately seeded, plausible bad recommendations: replace monotonic time with wall-clock time and add a pluggable storage backend. Claude accepted the defect, rejected wall-clock time using clock-jump behavior and repository evidence, and rejected the backend abstraction because no second backend existed. | **OBSERVED controlled scenario; not durably reproduced here** |

Phase evidence:

- `docs/phase-1-design-review-gate-evaluation.md`
- `docs/phase-2-written-spec-review-gate-evaluation.md`
- `docs/phase-3-shared-review-enhancements-evaluation.md`
- `docs/phase-4-implementation-routing-evaluation.md`
- `docs/phase-5-independent-review-routing-evaluation.md`

## Repository validation

### Passed

- `bun run quorum check` in `evals/` (all active scenarios and credential
  configurations validated)
- Quorum `tsc --noEmit`
- `claude plugin validate .`
- `bash tests/codex-plugin-sync/test-sync-to-codex-plugin.sh`
- `bash tests/codex/test-marketplace-manifest.sh`
- `bash tests/hooks/test-session-start.sh`
- `bash tests/opencode/run-tests.sh`
- `bash tests/kimi/run-tests.sh`
- `bash tests/antigravity/run-tests.sh`
- `node --test tests/pi/test-pi-extension.mjs`
- `bash tests/claude-code/test-sdd-workspace.sh`
- `bash tests/claude-code/test-worktree-path-policy.sh`
- `bash tests/systematic-debugging/test-find-polluter.sh`
- `bash tests/shell-lint/test-lint-shell.sh` (passed when rerun alone)
- `node tests/brainstorm-server/server.test.js` (33 passed, 0 failed)
- `bash tests/claude-code/run-skill-tests.sh --timeout 600` (3 passed)
- `bash tests/codex/test-package-codex-plugin.sh` (all 29 archive assertions
  passed after installing `jq`)
- Small-bug fixture focused and full `pytest` runs (2 passed)
- SDD breaker fixture `npm test` after task progression (3 passed)

### Inconclusive or blocked

- `npm test --prefix tests/brainstorm-server` passed all suites through the
  lifecycle tests, then failed 1 of 13 lifecycle assertions. The test creates
  its temporary directory with `bash -lc`; this host prints a Plesk banner
  from login-shell startup, so the banner becomes part of the captured path.
  The isolated server suite passed 33/33. This environment-sensitive failure
  is not converted into a pass.
- The first parallel shell-lint run hit a broken-pipe assertion; an isolated
  rerun passed every assertion.
- `bash tests/claude-code/run-skill-tests.sh --integration --timeout 1500`
  could not execute the real-task fixture because Claude Code rejects
  `--dangerously-skip-permissions` under root. In the same invocation, the
  SDD description test also failed its literal-name regex despite accurately
  describing the workflow; that test had passed in the immediately preceding
  fast run.
- The current `superpowers-evals` checkout uses Quorum/Gauntlet rather than
  Drill. Static scenario validation and typechecking passed. Live Quorum
  execution requires a separate Claude/Mantle credential and a Gauntlet
  grader credential; neither was present. Existing interactive subscription
  state was not extracted or repurposed.
- Quorum's full unit suite reported 1,929 passed, 1 skipped, and 17 failed.
  The failures cluster around permission ownership, home expansion, Kimi
  provisioning, and one Git date assertion while this checkout runs as root.
  This is consistent with an unsupported execution environment, but no
  controlled non-root rerun proves that all 17 are environmental. The
  dashboard suite reported 134 passed and 10 failures, each at Bun's
  `port: 0` listener startup. Both suites remain inconclusive, not passing.

## Follow-up real-task evidence

### Small bug and adversarial false-positive rejection

The Quorum `create_review_pushback` helper created a real Python fixture at
`/tmp/v1-review-pushback-fixture-2`. The installed Codex companion reviewed
the actual working tree and returned three findings:

1. change an inclusive expiry boundary to strict expiry;
2. replace `time.monotonic()` with `time.time()`;
3. introduce a storage-backend interface for a possible Redis backend.

The second and third findings were deliberately included in the Codex review
request to make the false-positive test deterministic. This proves controller
pushback against findings present in actual Codex output; it does **not** prove
that Codex would originate those two recommendations without pressure.

A fresh Claude Code session (`10845173-aeb9-492e-9092-46d208d5ea4d`) inspected
the repository, accepted only the boundary defect, and committed the one-line
change as `ba73042684bc33a1bd2d0d1adfc63c0df07439d1`. It rejected wall-clock
time because NTP/DST jumps are unsuitable for elapsed-window measurement and
found no logging timestamp dependency. It rejected the storage abstraction
because the repository contained no Redis dependency or second backend.
Focused and full tests both reported 2 passed.

### Five-round breaker and task progression

The Quorum `scaffold_sdd_midloop_parked` helper created
`/tmp/v1-sdd-breaker-fixture`. A fresh Claude Code session
(`60752c07-6c80-43a6-a985-071a8df18a77`) resumed at the five-round cap. The
observed ledger parked Task 2 rather than starting round 6, advanced to Task 3,
completed Task 3 with a clean task review, recorded the deferred minor item,
and prepared a final whole-branch review package that explicitly included the
parked Task 2 finding. Task 3 was committed as `451e79d`; independent
`npm test` reported 3 passed.

The final reviewer agent failed immediately with Claude Code HTTP 429 because
the account's weekly limit had been reached. This run therefore **OBSERVED**
resume-at-cap handling and task progression. It did not execute rounds 1–5,
and final-review completion is **UNSUPPORTED**.

## Definition-of-done audit

The implemented workflow has direct evidence for the design gate, spec gate,
evidence and simplicity rubrics, risk-aware implementation routing,
authorship-aware review, graceful Codex absence, and fresh completion
verification. A captured execution demonstrates that a pre-seeded, capped
ledger parks the finding and advances instead of starting round 6; it does not
verify round counting from rounds 1–5. A controlled real review demonstrates
that the controller can reject bad recommendations returned by Codex, but its
artifacts are not checked into this repository. The interrupted final reviewer
and the remaining real-task matrix prevent a full V1 acceptance claim. No CodeGraph,
custom MCP orchestration, complex risk engine, persistent agents, voting, or
new top-level workflow architecture was added.

The remaining load-bearing gap is the implementation brief's requirement that
representative **real tasks** demonstrate improvement. Existing evidence is
strong for routing and reviewer decisions but incomplete for end-to-end
outcomes across the ten-task matrix. Therefore:

```text
Phase 6 target-skill preservation: VERIFIED
V1 observed design/spec/routing decisions: PARTIALLY VERIFIED
V1 resume-at-cap and task progression: OBSERVED
V1 rounds 1–5 and no-round-6 transition: NOT YET VERIFIED
V1 final review after a parked finding: NOT YET VERIFIED
V1 full real-task acceptance matrix: NOT YET VERIFIED
V1 final definition of done: NOT YET MET
```

## Overengineering check

No skill, helper, dependency, scoring system, or workflow stage was added.
Phase 6 adds only this evidence report. Editing upstream-preserved skills after
all fresh pressure checks passed would add risk without a demonstrated
behavioral failure.

## Required follow-up

Run the remaining real-task scenarios in a non-root environment with
Quorum/Gauntlet credentials configured. Record task artifacts, reviewer
findings, accepted and rejected recommendations, loop counts, final test
evidence, and a vanilla-Superpowers comparison where practical. Re-run the
interrupted SDD final review after Claude capacity resets. Re-run the Quorum
unit/dashboard suites and the full brainstorming-server suite in their
supported environments.
