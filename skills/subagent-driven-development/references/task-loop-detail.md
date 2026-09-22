# Task Loop — Full Detail

Everything you paste into a dispatch prompt — and everything a subagent prints
back — stays resident in your context for the rest of the session and is re-read
on every later turn. Hand artifacts over as files.

## 1. Dispatch the implementer

Record BASE (`git rev-parse HEAD`) before dispatching — the review package and
fix-round diffs need it.

First classify the task using `model-selection.md`. For high-value/high-risk
work, dispatch the high-assurance route (see `codex-route.md`); otherwise
dispatch the standard route (`general-purpose`) with an explicit model tier. In
either case, fill the single [../implementer-prompt.md](../implementer-prompt.md)
contract — do not maintain a second route-specific task prompt.

- **Task brief:** before dispatching, run this skill's
  `scripts/task-brief PLAN_FILE N` — it extracts the task's full text to a
  uniquely named file and prints the path. Compose the dispatch so the brief
  stays the single source of requirements. Your dispatch should contain:
  (1) one line on where this task fits in the project; (2) the brief path,
  introduced as "read this first — it is your requirements, with the exact
  values to use verbatim"; (3) interfaces and decisions from earlier tasks that
  the brief cannot know; (4) your resolution of any ambiguity you noticed in the
  brief; (5) the report-file path and report contract. Exact values (numbers,
  magic strings, signatures, test cases) appear only in the brief. Never make a
  subagent read the whole plan file.
- **Report file:** name the implementer's report file after the brief
  (brief `…/task-N-brief.md` → report `…/task-N-report.md`) and put it in the
  dispatch prompt. The implementer writes the full report there and returns only
  status, commits, a one-line test summary, and concerns.
- A dispatch prompt describes one task, not the session's history. Do not paste
  accumulated prior-task summaries ("state after Tasks 1-3") into later
  dispatches — a real session's dispatch hit 42k chars of which 99% was pasted
  history. A fresh subagent needs its task, the interfaces it touches, and the
  global constraints. Nothing else.
- If an earlier task parked a finding in the area this task touches, carry a
  pointer to that ledger entry in the dispatch.
- Record the implementer's agent identity from the dispatch result — fix-loop
  rounds 1-3 resume this agent.
- Never dispatch multiple implementation subagents in parallel (conflicts).

## 2. Handle the report

Implementer subagents report one of four statuses:

**DONE:** Generate the review package (`scripts/review-package PLAN_FILE BASE
HEAD`, from this skill's directory — it prints the unique file path it wrote;
BASE is the commit you recorded before dispatching — never `HEAD~1`, which
silently drops all but the last commit of a multi-commit task), then dispatch
the task reviewer with the printed path.

**DONE_WITH_CONCERNS:** The implementer completed the work but flagged doubts.
Read the concerns before proceeding. If they are about correctness or scope,
address them before review. If they are observations (e.g. "this file is
getting large"), note them and proceed to review.

**NEEDS_CONTEXT:** Provide the missing context and re-dispatch.

**BLOCKED:** Assess the blocker:
1. A context problem → provide more context, re-dispatch with the same model
2. Needs more reasoning → re-dispatch with a more capable model
3. Task too large → break it into smaller pieces
4. The plan itself is wrong → escalate to the human

**Never** ignore an escalation or force the same model to retry without
changes. If the implementer said it's stuck, something needs to change.

If the implementer asks questions — before starting or mid-task — answer
clearly and completely, provide additional context if needed, and don't rush it
into implementation.

## 3. Review the task

Per-task reviews are task-scoped gates. The broad review happens once, at the
final whole-branch review. Never skip the task review, and never accept a
report missing either verdict — spec compliance AND task quality are both
required. Implementer self-review never replaces the task review; both are
needed.

- Select the reviewer using Independent review routing in `model-selection.md`
  and record its agent identity for any scoped re-review. Fill the same
  [../task-reviewer-prompt.md](../task-reviewer-prompt.md) for either route.
  For the Codex route, see `codex-route.md`.
- Hand the reviewer its diff as a file: run
  `scripts/review-package PLAN_FILE BASE HEAD` and pass the printed path (or,
  without bash: `git log --oneline`, `git diff --stat`, and `git diff -U10` for
  the range, redirected to one uniquely named file). The output never enters
  your own context, and the reviewer sees the commit list, stat summary, and
  full diff in one Read call. Never dispatch a task reviewer without a diff
  file.
- **Reviewer inputs:** three paths — the brief file, the report file, and the
  review package — plus the global constraints that bind the task.
- The global-constraints block is the reviewer's attention lens. Copy the
  binding requirements verbatim from the plan's Global Constraints section or
  the spec: exact values, exact formats, and the stated relationships between
  components ("same layout as X", "matches Y"). The reviewer's template already
  carries the process rules (YAGNI, test hygiene, review method) — the
  constraints block is for what THIS project's spec demands.
- Do not add open-ended directives like "check all uses" or "run race tests if
  useful" without a concrete, task-specific reason.
- Do not ask a reviewer to re-run tests the implementer already ran on the same
  code — the implementer's report carries the test evidence.
- Do not pre-judge findings for the reviewer — never instruct a reviewer to
  ignore or not flag a specific issue. If you believe a finding would be a false
  positive, let the reviewer raise it and adjudicate it in the review loop. If
  the prompt you are writing contains "do not flag," "don't treat X as a
  defect," "at most Minor," or "the plan chose" — stop: you are pre-judging,
  usually to spare yourself a review loop.

The task reviewer may report "⚠️ Cannot verify from diff" items — requirements
that live in unchanged code or span tasks. These do not block the rest of the
review, but you must resolve each one yourself before marking the task
complete: you hold the plan and cross-task context the reviewer lacks. If you
confirm an item is a real gap, treat it as a failed spec review — it enters the
fix loop with the other findings.
