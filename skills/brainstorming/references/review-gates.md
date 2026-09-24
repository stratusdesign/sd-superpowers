# Simplicity, Defect Handling, and the Review Gates

## Simplicity and Defect Handling (governing rules)

These govern design, synthesis, and every review gate below — they are not optional reviewer advice.

**Simplicity is the default.** Select the simplest complete solution. Every added component, abstraction, dependency, configuration option, workflow stage, or requirement carries the burden of proof — justified only by confirmed actor needs, confirmed constraints, observable success conditions, or necessary correctness, security, or maintainability. Do not add architecture for hypothetical scale, possible future consumers, unrequested extensibility, abstract purity, model preference, or convention with no current need. When two solutions both fully satisfy the requirement, choose the simpler. Simple never means incomplete, fragile, insecure, or untestable — it means no unjustified machinery. Visible process scales with complexity too: a trivial task gets a compact actor statement, brief discovery (a single question or short list as warranted — sometimes none beyond a confirmation), and a concise design; a cross-platform or agentic system may need the full process.

**Subtractive before additive.** For each material finding, evaluate remedies in order — **Delete → Narrow → Simplify → Reuse → Clarify → Add only what remains necessary.** Before recommending anything additive, ask: can the problematic scope be removed; is the defect caused by unnecessary scope or an invented/unconfirmed requirement; can an existing mechanism solve it; can the failure state be made impossible; would the fix cost more complexity than the defect warrants? A missing capability is a defect only when a confirmed actor, approved requirement, stated constraint, observable success test, or necessary correctness/security demands it. Reject additive recommendations that have no confirmed need.

Every Critical or Important finding reports the confirmed requirement affected and the smallest valid correction. A finding whose correction ADDS something reports two more lines — the subtractive option considered, and why adding is still necessary:

```text
Finding:
Confirmed requirement affected:
Smallest valid correction:
(additive corrections only) Subtractive option considered:
(additive corrections only) Why an additive change is still necessary:
```

Do not force this onto Minor findings, where it costs more than it returns.

## Design Review Gate

Run this gate once the project context is inspected, intent and constraints are understood, approaches have been explored, and the complete candidate design is coherent. Do not run it on every message or unfinished design section.

Use [../design-reviewer-prompt.md](../design-reviewer-prompt.md) to dispatch two independent read-only reviewers filling distinct roles:

1. a constructive reviewer; and
2. an adversarial reviewer.

Bind these roles to available routes — a fresh same-family context and/or the `codex:codex-rescue` subagent — preferring a different model family for at least one where practical. Both reviews are read-only and capability-dependent. If a reviewer capability is absent before dispatch, apply that reviewer's rubric yourself, tell the user which independent perspective was unavailable, and continue — never invent a command or block the workflow. If an available reviewer fails to start, authenticate, finish, or return usable output, report the actionable failure and ask whether to retry or proceed with an explicitly degraded self-review. Do not silently substitute your own answer for a failed invocation.

**Synthesis:**

- Compare both reviews with the user's stated intent and constraints.
- Verify load-bearing factual disputes against repository evidence or authoritative documentation where practical. Mark material claims `VERIFIED`, `INFERRED`, or `UNSUPPORTED` when that distinction helps the decision.
- Apply **Simplicity and Defect Handling** above: ask whether this can be materially simpler while still fully solving the current requirement, and resolve each finding subtractive-first (Delete → Narrow → Simplify → Reuse → Clarify → Add only what remains). Remove premature abstractions, speculative extensibility, and unnecessary interfaces, adapters, factories, service layers, dependencies, or configuration. Simple must remain correct and maintainable.
- Accept evidence-backed findings; reject unsupported, invented, or preference-only findings. An independent reviewer is an input, not final authority. Do not vote.
- Present the synthesized design and explain material accepted or rejected findings before asking for final user approval.

Run at most one targeted second review using the scoped contract in [../design-reviewer-prompt.md](../design-reviewer-prompt.md), and only when a Critical finding remains, an important factual dispute is unresolved, or synthesis materially changed the design and needs re-checking. Dispatch only the reviewer needed for that issue. Minor findings never trigger another pass. If a material issue remains after the targeted pass, surface it to the user instead of starting a debate loop.

User-requested revisions after synthesis restart the bounded gate only when they produce a materially different coherent candidate. Minor corrections return directly to final approval. Withholding approval by itself does not restart reviewers.

## After the Design

**Documentation:**

- Write the validated design (spec) to `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`
  - (User preferences for spec location override this default)
- Use elements-of-style:writing-clearly-and-concisely skill if available

**Spec Self-Review:**
After writing the spec document, look at it with fresh eyes:

1. **Placeholder scan:** Any "TBD", "TODO", incomplete sections, or vague requirements? Fix them.
2. **Internal consistency:** Do any sections contradict each other? Does the architecture match the feature descriptions?
3. **Scope check:** Is this focused enough for a single implementation plan, or does it need decomposition?
4. **Ambiguity check:** Could any requirement be interpreted two different ways? If so, pick one and make it explicit.

Fix any issues inline. No need to re-review — just fix and move on.

## Written Spec Review Gate

After self-review, dispatch a fresh, read-only independent review using [../spec-document-reviewer-prompt.md](../spec-document-reviewer-prompt.md). The central question is: **Could another competent coding agent implement this specification without making material assumptions?**

The independent review must challenge Critical defects, ambiguity, unsupported or hallucinated claims, missing acceptance criteria and tests, invented requirements, unnecessary complexity, missing edge cases, and unimplementable dependencies. It must ask whether the requirement can be solved materially more simply without becoming brittle or incomplete.

Bind the independent reviewer role to an available route — the `codex:codex-rescue` subagent or a fresh separate context — preferring a different model family where practical. Apply the same capability-absence and invocation-failure handling as the Design Review Gate: disclose degraded self-review when capability is absent; for setup, authentication, dispatch, completion, or result failure, report the failure and ask whether to retry or explicitly continue degraded. Never fabricate reviewer output.

Synthesize findings into the spec before asking the user to review it:

- Before canonicalizing the spec, identify and verify its load-bearing technical claims and repository assumptions, including every one flagged by review, against repository evidence, observed output, or authoritative documentation. If verification is not practical, label the claim `UNSUPPORTED` and resolve it with the user; never present it as fact or silently proceed.
- Resolve Critical findings, ambiguities, missing acceptance criteria, missing tests, and implementability gaps.
- Reject unsupported, invented, or preference-only findings that conflict with approved intent.
- Ask: **Can this be materially simpler while still fully solving the current requirement?** Resolve findings subtractive-first per **Simplicity and Defect Handling** above.
- The SA owns the canonical spec. The independent reviewer reports findings; it does not rewrite the spec or make the final decision.

Use at most one targeted re-check, only for an unresolved Critical finding or material factual dispute after synthesis. Minor or advisory findings do not trigger another pass. If a material issue remains, resolve it with the user instead of starting another reviewer loop.

Commit the canonical spec after synthesis and any targeted re-check or user resolution. Verify the committed file contains the reviewed version before asking for approval.

## User Review Gate

After the written spec review gate passes, ask the user to review the canonical spec before proceeding:

> "Spec written and committed to `<path>`. Please review it and let me know if you want to make any changes before we start writing out the implementation plan."

Wait for the user's response. If they request changes, make them and re-run self-review plus the written spec review gate. Only proceed once the user approves.
