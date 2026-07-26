# V1 Multi-Agent Workflow Handover

**Date:** 2026-07-26  
**Repository:** `stratusdesign/sd-superpowers`  
**Working branch:** `v1-real-task-validation`  
**Branch base:** fork `origin/main` at `bf58630`  
**Latest commit before this handover:** `0e98b46`

## Executive status

The V1 Claude + Codex workflow changes are implemented and merged into the
fork's `main` branch through Phase 6. The current feature branch adds follow-up
real-task validation evidence; it has not been pushed.

The workflow implementation is complete for the agreed V1 scope. Validation is
partial. Do not describe V1 as fully validated until the outstanding
end-to-end scenarios below have run and produced durable evidence.

```text
Phase 1 — design-review gate: implemented
Phase 2 — written-spec review gate: implemented
Phase 3 — shared evidence and simplicity review: implemented
Phase 4 — implementation routing: implemented
Phase 5 — independent review routing: implemented
Phase 6 — preservation and acceptance audit: implemented

Full real-task acceptance matrix: incomplete
V1 final definition of done: not yet met
```

## Repository safety

The only writable remote is the fork:

```text
origin   = https://github.com/stratusdesign/sd-superpowers.git
upstream = https://github.com/obra/superpowers.git
```

Never push branches, commits, tags, or pull requests to
`obra/superpowers`. These are intentionally fork-specific workflow changes and
do not belong in upstream core.

Before any push, verify:

```bash
git remote -v
git remote get-url --push origin
git branch --show-current
git status --short
```

Push only with:

```bash
git push -u origin "$(git branch --show-current)"
```

## Implemented behavior

### Design and specification

- A coherent candidate design receives constructive Claude review and
  adversarial Codex review before approval.
- The primary agent verifies and synthesizes findings against user intent;
  neither reviewer is treated as final authority.
- Reviews explicitly challenge unsupported technical claims and unnecessary
  complexity.
- Review repeats are targeted and bounded.
- The written specification receives its own Codex completeness and
  adversarial review after the design is approved.
- Codex absence or invocation failure degrades explicitly to the available
  Claude route; the workflow never claims a Codex result that does not exist.

### Implementation and review routing

- High-value or high-risk tasks route to the installed official Codex
  implementation capability.
- Routine and mechanical tasks remain on an economical Claude route.
- Reviewer selection considers implementation authorship and risk:
  Codex-authored work receives fresh Claude review, while qualifying
  Claude-authored work can receive fresh Codex review.
- Codex requests run in the foreground and require a usable terminal result
  before the controller proceeds.
- Existing scoped fix/re-review behavior and the five-round cap are preserved.

### Completion

- Existing `verification-before-completion` and
  `finishing-a-development-branch` skills remain unchanged from
  `upstream/main`.
- No CodeGraph, custom MCP orchestration, persistent agents, voting, external
  memory, or complex risk-scoring system was added.

## Validation completed

Detailed evidence is in:

- `docs/phase-1-design-review-gate-evaluation.md`
- `docs/phase-2-written-spec-review-gate-evaluation.md`
- `docs/phase-3-shared-review-enhancements-evaluation.md`
- `docs/phase-4-implementation-routing-evaluation.md`
- `docs/phase-5-independent-review-routing-evaluation.md`
- `docs/phase-6-final-verification.md`

Observed results include:

- Design and specification gates rejecting invented APIs, methods,
  configuration, paths, and requirements.
- Simplicity checks removing speculative interfaces, adapters, factories,
  service layers, and dependencies.
- Expected implementation and review routing for mechanical, cross-file, and
  security-sensitive scenarios.
- Graceful handling of missing or failed Codex invocation.
- A controlled real Python bug fixture in which Claude accepted a genuine
  boundary defect and rejected two plausible but deliberately seeded bad
  recommendations returned by Codex.
- A pre-seeded SDD ledger at the five-round cap in which the controller parked
  the open finding and progressed to the next task instead of starting round
  6.
- Codex package archive suite: all 29 assertions passed.
- Brainstorm server isolated suite: 33 passed.
- Quorum scenario validation and TypeScript typechecking passed.

The real-task fixtures and Claude sessions are external observations under
`/tmp` and the local harness. They are not durable repository evidence and
must not be upgraded to `VERIFIED` acceptance claims without sanitized,
reproducible artifacts.

## Outstanding work

### 1. Complete the real-task acceptance matrix

Run end-to-end tasks for the remaining scenario categories:

1. simple feature;
2. cross-file feature;
3. architectural change;
4. ambiguous requirement;
5. API or library hallucination;
6. overengineering temptation;
7. security or data-sensitive change;
8. mechanical low-risk task.

The small-bug and bad-Codex-recommendation scenarios have useful observations,
but should also be repeated through the durable harness.

For every task, retain:

- initial prompt and fixture revision;
- selected implementation and review routes;
- reviewer reports;
- accepted and rejected findings with reasons;
- fix-loop count and ledger;
- final diff and commits;
- exact verification commands and output;
- final disposition.

### 2. Exercise the complete five-round loop

The observed SDD run resumed from a ledger already at round 5. It verified the
resume-at-cap transition only.

A new run must:

1. begin before round 1;
2. execute and record rounds 1 through 5;
3. prove no round 6 starts;
4. persist the parked finding;
5. pass that finding into final whole-branch review;
6. complete the final review and final verification.

### 3. Finish the interrupted final review

The previous final reviewer stopped immediately with Claude Code HTTP 429
after the weekly limit was reached. Repeat after capacity resets. Do not infer
the final-review result from the successful task tests.

### 4. Run live Quorum/Gauntlet evaluations

The public `superpowers-evals` checkout now uses Quorum/Gauntlet rather than
the older Drill workflow. Static checks ran, but live evaluation needs:

- a supported non-root execution environment;
- a separately configured Claude or Mantle credential for the coding agent;
- a Gauntlet grader credential;
- Bun available normally on `PATH`.

Do not extract, copy, or repurpose interactive host subscription secrets to
bypass the harness credential boundary.

### 5. Re-run environment-sensitive suites

Run these in a supported non-root environment:

- Quorum unit suite: last run reported 1,929 passed, 1 skipped, 17 failed.
  Failures clustered around permission ownership, home expansion, Kimi
  provisioning, and one Git date assertion.
- Quorum dashboard suite: last run reported 134 passed and 10 listener-startup
  failures at Bun `port: 0`.
- Full brainstorm-server suite: one lifecycle test failed because this host's
  login-shell Plesk banner contaminated a temporary path captured from
  `bash -lc`.

Keep these results inconclusive until controlled reruns distinguish
environment limitations from repository defects.

### 6. Add durable evidence

Check in sanitized evaluation artifacts or an equivalent reproducible result
bundle. Avoid raw session logs containing credentials, home-directory
metadata, or unrelated user data.

At minimum, durable evidence should connect each acceptance claim to:

- fixture setup;
- prompt;
- relevant transcript excerpt;
- source diff;
- test output;
- environment and tool versions.

### 7. Compare with vanilla Superpowers

Where practical, run equivalent scenarios against unmodified
`upstream/main`. The comparison should demonstrate behavioral improvement, not
merely show that the fork follows its new instructions.

## Recommended next sequence

1. Wait for Claude capacity to reset.
2. Prepare a supported non-root Quorum/Gauntlet environment and credentials.
3. Repeat the capped SDD scenario from round 1 through final review.
4. Run the remaining real-task matrix.
5. Save sanitized artifacts under a dedicated evaluation-evidence directory.
6. Re-run the three environment-sensitive suites.
7. Update the Phase 6 report and this handover from observed outputs.
8. Run an independent diff review.
9. Ask the human partner to inspect the complete diff before any pull request.

## Non-goals

Do not expand the remaining work into:

- a general multi-agent platform;
- new standalone workflow skills without behavioral evidence;
- CodeGraph or custom MCP orchestration;
- persistent agents or external memory;
- model voting;
- complex risk scoring;
- broad rewrites of existing Superpowers skills;
- an upstream pull request for these fork-specific changes.

## Current branch handoff

At the start of this handover:

```text
branch: v1-real-task-validation
base:   origin/main at bf58630
head:   0e98b46
push:   not pushed
```

The next contributor should first read `WORKFLOW-MAP.md`,
`V1-IMPLEMENTATION-BRIEF.md`, and the phase evaluation documents. Treat
`docs/phase-6-final-verification.md` as the authoritative detailed record and
this document as the operational summary.
