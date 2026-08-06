---
name: requesting-code-review
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements
---

# Requesting Code Review

Dispatch a code reviewer subagent to catch issues before they cascade. The reviewer gets precisely crafted context for evaluation — never your session's history.

**Core principle:** Review early, review often.

## When to Request Review

**Mandatory:**
- After each task in subagent-driven development
- After completing major feature
- Before merge to main

**Optional but valuable:**
- When stuck (fresh perspective)
- Before refactoring (baseline check)
- After fixing complex bug

## How to Request

**1. Get git SHAs:**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. Dispatch code reviewer subagent:**

The reviewer is a role, not a model brand. Choose the route by available
capability, diff complexity and risk, authorship independence, and model-family
independence where materially useful — always a fresh context, never the
implementation context. Within Claude Code the route resolves to
`general-purpose` (with an explicit model per subagent-driven-development Model
Selection) or `codex:codex-rescue` (model/effort omitted unless the user chose
them); do not invent unsupported routes. Prefer the cross-family route when the
change is risky or consequential enough to justify it, not merely for symmetry.
Fill the template at [code-reviewer.md](code-reviewer.md).

**Placeholders:**
- `[REVIEWER_AGENT]` / `[MODEL]` - the chosen route and its model per above
- `{DESCRIPTION}` - Brief summary of what you built
- `{PLAN_OR_REQUIREMENTS}` - What it should do
- `{BASE_SHA}` - Starting commit
- `{HEAD_SHA}` - Ending commit

**3. Verify and adjudicate findings (do not auto-apply):**
- Verify each Critical and Important finding against the requirements and evidence
- Apply valid findings; reject unsupported, invented, or preference-only findings, with reasoning
- Note Minor issues for later
- The SA/controller owns the decision — there is no model vote, and no blanket "fix every Critical and Important finding"

## Example

```
[Just completed Task 2: Add verification function]

You: Let me request code review before proceeding.

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[Dispatch code reviewer subagent]
  DESCRIPTION: Added verifyIndex() and repairIndex() with 4 issue types
  PLAN_OR_REQUIREMENTS: Task 2 from docs/superpowers/plans/deployment-plan.md
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661

[Subagent returns]:
  Strengths: Clean architecture, real tests
  Issues:
    Important: Missing progress indicators
    Minor: Magic number (100) for reporting interval
  Assessment: Ready to proceed

You: [Verify "Missing progress indicators" against the requirements — the plan
     does require progress reporting, so the finding is valid. Apply it. Had it
     been an unrequested addition, reject it with reasoning instead.]
[Continue to Task 3]
```

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "I'll just review the diff myself instead of dispatching a reviewer" | You're the coordinator — reviewing the diff inline burns the context window you need to keep driving the work. Dispatch a reviewer subagent: the diff and the evaluation live in its context, and only the findings come back to you. |
| "The reviewer needs my whole session history to understand the change" | Hand it precisely crafted context, never your session's history. That keeps the reviewer on the work product, not your thought process. |

## Red Flags

**Never:**
- Skip review because "it's simple"
- Ignore a verified Critical finding
- Proceed past a verified Important finding without adjudicating it
- Argue with valid technical feedback

**If reviewer wrong:**
- Push back with technical reasoning
- Show code/tests that prove it works
- Request clarification

See template at: [code-reviewer.md](code-reviewer.md)
