# Independent Co-design (design partner, not reviewer)

**Roles are defined independently of model brand.** The same rules hold whichever model fills a role:

- **Primary System Agent (SA):** owns user dialogue, orchestration, evidence gathering, synthesis, adjudication, the canonical design and spec, and every final decision.
- **Independent co-designer:** works from a fresh context and a neutral brief; proposes discovery questions, develops independent approaches, names assumptions and trade-offs. It does NOT make the final decision.
- **Reviewer / implementer / verifier** are defined in the review gates (see `review-gates.md`) and in subagent-driven-development.

Verification comes from repository evidence, version-control history, tests, type/compiler checks, runtime behaviour, authoritative documentation, and observed tool output — a model statement is not verification by itself.

**Capability bindings (Claude Code today).** The SA is the parent session. An independent role may be filled by a fresh separate context of the same family, or by the `codex:codex-rescue` subagent. Where practical, use a DIFFERENT model family for the independent role; when only one family is available, use a fresh isolated context and disclose that model-family independence was unavailable. In another harness, bind these same roles to the routes that actually exist there — never invent a route (e.g. a reverse dispatch) that is not installed and verified merely to make the prose symmetrical.

## When to run co-design — BOTH must hold

1. an independent capability is available, AND
2. a material open design decision would benefit from a second constructive perspective — multiple actors or surfaces; materially different actor capabilities; consequential or hard-to-reverse architecture; APIs, protocols, agent interfaces, or tool ergonomics; several credible approaches with real trade-offs; significant uncertainty; the independent model is itself a user or implementer of the result; the user asked for multi-model design; or low SA confidence in the preferred approach.

SKIP it — even when a second model is available — for routine or mechanical work, trivially different approaches, no open material decision, or when the later review gate is sufficient challenge. Never invoke for symmetry. When skipped, the SA still performs proportionate actor, requirement, and success-condition discovery, and the review gates remain available where justified. Revisit the decision if discovery later exposes a material decision.

## When it runs, it spans discovery and approaches — BOTH generated blind

**"Co-design active" is a state, not a step.** It does not do work before discovery. When it holds: the *Ask clarifying questions* step runs blind SA + independent question generation, then merge/filter/present, BEFORE the user answers; then, after the user's answers, the *Propose 2-3 approaches* step runs blind SA + independent approach generation, then synthesis. Order is always discovery questions → user answers → approach generation, co-design on or off.

1. **Independent question generation (during discovery).** The SA drafts the questions it believes must be answered. The independent co-designer receives the same neutral brief and drafts its own. Neither sees the other's list before producing its own. Then the SA does NOT present every question — raw model output can be long (an independent probe once produced 14). The SA: merges the two sets; deduplicates; drops questions already answered by inspected evidence; drops low-value curiosity; drops questions whose answers would not materially change the design; orders what remains naturally; and presents the smallest useful set. **Keep a question only if its answer could materially change actor coverage, scope, constraints, success conditions, architecture, implementation feasibility, security, or failure handling.** There is no numeric maximum — the rule is: ask the smallest set of questions needed to prevent material assumptions. Lists and small batches remain allowed. The SA never answers an unresolved question itself and never converts missing information into an invented requirement. Material answers may prompt targeted follow-ups from either side.
2. **Independent approach generation (after discovery).** The SA develops its approaches and provisional recommendation; the independent co-designer develops approaches from the same neutral brief; neither sees the other's conclusions first. The SA then compares and synthesizes into the simplest complete candidate, before presenting candidate design sections.

## Neutral brief — prevents SA anchoring

The independent role receives: the user's verbatim statements or faithful summaries, confirmed actor coverage, confirmed constraints and success conditions, relevant repository files and observed facts, the unresolved facts and user-originated open questions (never either side's drafted discovery-question list — that stays private until both lists are complete), and authoritative external evidence where applicable. It must NOT receive: the SA's preferred solution or recommendation, a defence of any approach, another model's conclusions, or case-specific steering about what to flag or not flag. The fixed role rubric — the standing list of what a reviewer's or co-designer's role checks for — is not steering and is always allowed; the ban is on advocacy and per-finding steering, not on telling a role what its job is. This anti-anchoring rule applies to co-design AND to later review.

## Synthesis — material disagreement only

Adopt evidence-backed positions; where you reject one, record why. MATERIAL disagreement is design evidence — different assumptions, conflicting evidence, different actor needs, a consequential trade-off, feasibility uncertainty, or different failure/security modes. Ignore wording, style, preference-only redesign, equivalent solutions with no material consequence, and speculative concerns outside confirmed scope. Explain material accepted and rejected positions where it helps the user understand the design. You own the synthesis; do not vote between models, and do not build an exhaustive disagreement ledger.

## Reviewer independence

The same model may co-design here and adversarially review later, but the later review runs in a FRESH context with the neutral review brief (approved intent, confirmed actor coverage, constraints, repository evidence, and the coherent candidate) — NOT the earlier co-design response, your defence of the chosen design, or commentary steering what it should or should not flag. This is not perfect independence, but it reduces anchoring and prevents the review from merely reaffirming its earlier position.

## Degradation

If no independent capability exists, state that the independent perspective was unavailable and continue — use a fresh isolated self-review where useful; never fabricate a second-model result, never block. If a dispatch was attempted and failed (setup, auth, dispatch, completion, or unusable output), report the actionable failure and ask whether to retry or explicitly continue with reduced independence — same rules as the Design Review Gate; never silently substitute your own answer for a failed invocation.
