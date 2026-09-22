---
name: subagent-driven-development
description: Use when executing implementation plans with independent tasks in the current session
---

# Subagent-Driven Development

Execute a plan by dispatching a fresh implementer subagent per task, a task review (spec compliance + code quality) after each, and a broad whole-branch review at the end.

**Why subagents:** each task gets an isolated context built by you — never your session's history. This keeps implementers focused and preserves your own context for coordination.

**Core principle:** fresh subagent per task + task review (spec + quality) + broad final review = high quality, fast iteration.

**Continuous execution:** do not pause to check in with your human partner between tasks. The only reasons to stop: a BLOCKED status you cannot resolve, ambiguity that genuinely prevents progress, or all tasks complete. "Should I continue?" prompts waste their time. Between tool calls, narrate at most one short line — the ledger and tool results carry the record.

**When to use:** you have an implementation plan, the tasks are mostly independent, and the work stays in this session. Parallel session instead → superpowers:executing-plans. No plan → brainstorm first. Read `references/process-flow.md` when unsure how a state re-enters the loop.

**Foreman-present:** if your session brief names a seat, dispatch, review commissioning, fix-loop routing, and completion acknowledgement all belong to the foreman — you supply artifacts and requests only. Report handling stays with you.

## Setup

1. Isolated workspace via superpowers:using-git-worktrees. Never implement on main/master without explicit consent.
2. Run `scripts/sdd-workspace PLAN_FILE` — it prints this plan's git-ignored artifact directory (ledger, briefs, reports, review packages). Another plan's directory is never yours.
3. Ledger at `<workspace>/progress.md`, first line `# SDD ledger — plan: <plan file path>`. Conversation memory does not survive compaction; controllers without a ledger have re-dispatched entire completed task sequences. If a ledger exists and names your plan: tasks with a `Task <N>: complete` line are DONE — resume at the first task without one; a task ending in a fix-round line resumes mid-loop. A ledger naming a different plan is not yours — leave it, start fresh. After compaction, trust the ledger and `git log` over recollection.
4. Read the plan once, note its Global Constraints, create a todo per task.
5. Pre-flight scan: tasks that contradict each other, the constraints, or the review rubric go to your human partner as ONE batched question — each finding beside the plan text that mandates it — before execution. Clean scan → proceed without comment.

## Model Selection

Route by value and risk, then pick the least powerful model that can do the job — and always name the model explicitly on `general-purpose` dispatches. High-value or high-risk work takes the high-assurance route. **Read `references/model-selection.md` before your first dispatch**; read `references/codex-route.md` whenever a dispatch or review uses the Codex route.

## The Task Loop

Hand artifacts over as files — everything pasted into or printed by a dispatch stays in your context forever. **Read `references/task-loop-detail.md` before dispatching Task 1** (and `references/example-workflow.md` if this is your first SDD run). All artifacts you write — briefs, dispatches, ledger entries — are clear, factual, and to the point: no unnecessary jargon, no wasteful sentences.

1. **Dispatch the implementer.** Record BASE (`git rev-parse HEAD`). Extract the brief with `scripts/task-brief PLAN_FILE N`; the brief is the single source of requirements. Dispatch = task's place in the project + brief path + earlier-task interfaces + your ambiguity resolutions + report-file path. Never paste plan or session history. Record the agent's identity. Never run implementers in parallel.
2. **Handle the report.** DONE → review. DONE_WITH_CONCERNS → read the concerns first. NEEDS_CONTEXT → supply it, re-dispatch. BLOCKED → change something (context, model, task size, or escalate) — never force an unchanged retry.
3. **Review the task.** Never skip it; spec compliance AND quality verdicts both required — implementer self-review replaces neither. Build the diff with `scripts/review-package PLAN_FILE BASE HEAD` (never `HEAD~1`). Reviewer gets brief + report + package + the plan's constraints verbatim. Never pre-judge findings ("do not flag…" = stop). Resolve any "⚠️ cannot verify" items yourself.
4. **Fix loop** — triggers on spec ❌, Critical, Important, or a confirmed ⚠️ gap. **Read `references/fix-loop.md` the first time it triggers.** Skeleton: minors → ledger, deferred; plan-conflicting findings → ask the human which governs; five rounds max — rounds 1-3 resume the implementer, 4-5 fresh implementer on a stronger model; every round ends in a scoped re-review; never fix in the controller session; at the cap, adjudicate each finding — park with a ruling, or STOP as BLOCKED if load-bearing. Every ruling is a ledger entry; silent discards are forbidden.
5. **Complete.** Ledger line `Task <N>: complete (commits <base7>..<head7>, review clean)` (or `<K> parked`), mark the todo, move on. Never advance past open, unparked Critical/Important findings.

## Final Review and Finish

When no tasks remain, **read `references/final-review.md`**: whole-branch review on the most capable model, one fix wave, one scoped re-review, adjudicate residuals. When clean: delete this plan's workspace and use superpowers:finishing-a-development-branch.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Close enough on spec compliance" | Reviewer found spec gaps = not done. Fix or hit the cap and adjudicate — those are the only exits. |
| "I'll fix it myself, dispatching is overhead" | Controller fixes pollute your context and skip review. Resume the implementer. |
| "One more round will converge" | Past the cap, rounds don't converge — the failure is structural. Adjudicate and route. |
| "The reviewer will just find something new anyway" | Scoped re-reviews verify fixes; they cannot wander. New findings on untouched code go to the ledger, not the loop. |
| "This finding is obviously wrong, I'll drop it" | You adjudicate only at the cap, and every ruling is a ledger entry. Silent discards are forbidden. |
| "The fix was small, skip the re-review" | Unreviewed fixes are how regressions land. Every round ends with a scoped re-review. |
| "Reviews slow the loop down" | The loop without reviews is just unverified churn. Reviews are the loop's brakes and steering. |
| "Ledger bookkeeping is overhead" | The ledger is what survives compaction. Controllers without one have re-dispatched entire completed task sequences. |
