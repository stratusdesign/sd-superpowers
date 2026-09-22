# Codex Route Mechanics (`codex:codex-rescue`)

The installed Codex subagent is a write-capable implementation route, not a
model name for `general-purpose`. Do not invent a Codex model identifier or put
Codex in the Claude `model` field. Leave Codex model and effort unset unless the
user explicitly chose them.

## Dispatch flags

- **Fresh implementer or fresh reviewer:** prepend `--wait --fresh` to the
  initial filled prompt, so SDD receives a terminal result rather than a
  background-job acknowledgement.
- **Fix rounds 1-3 (resuming the implementer):** prepend `--wait --resume` so
  the same Codex thread finishes the fix in the foreground.
- **Fix rounds 4-5 (escalation):** prepend `--wait --fresh`; the fresh thread is
  the escalation.
- **Reviewer dispatches:** state explicitly that the task is read-only.

Fill the same prompt templates as the standard route — implementer, task
reviewer, or re-reviewer. Do not maintain a second route-specific prompt.

## Lifecycle gate (initial dispatch)

Do not proceed to task review until the foreground dispatch has returned a
usable terminal result, the report file exists with the required test evidence,
and the recorded BASE..HEAD range contains the claimed commit(s). A background
job acknowledgement is not `DONE`.

## The `.git/index.lock` exception

The Codex write sandbox may complete files and tests but be unable to write
`.git/index.lock`. Accept this one exception only when the terminal result and
report name that exact Git-metadata restriction, the worktree contains only the
task's expected changes, `git diff --check` passes, and the reported focused
tests pass when the controller reruns them. The controller then commits those
unchanged files and records the SHA before review. The controller must not edit
the implementation while doing this. Any other missing artifact, blocker, or
nonterminal result follows the failure handling in `model-selection.md`.

A Codex `DONE_WITH_CONCERNS` caused only by this verified restriction gets the
same controller-commit treatment before the review package is generated.

## Lifecycle gate (every fix round)

Repeat the gate before each re-review: require a usable foreground terminal
result, validate the appended report and its focused test evidence, and verify
FIX_BASE..HEAD contains the claimed commit(s). If the only failure is the exact
`.git/index.lock` restriction, apply the same controller validation and commit
the unchanged fix artifacts before building the review package. Stop on every
other missing, stale, nonterminal, or blocked result; never consume a review
round with an empty or stale diff.

## Review-side rules

- A cross-family reviewer must be a different thread from the implementer that
  produced the work. Never resume a cross-family implementation thread for
  review.
- **Cross-family authored fix:** dispatch a fresh same-family
  (`general-purpose`) reviewer with an explicit model. Do not resume a prior
  cross-family reviewer.
- **Same-family authored fix routed cross-family:** if the prior reviewer was on
  the cross-family route, resume it with `--wait --resume`; otherwise dispatch a
  fresh cross-family reviewer with `--wait --fresh`.
