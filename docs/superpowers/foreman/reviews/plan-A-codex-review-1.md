# Plan A (methodology roles update) — Codex adversarial review, round 1

> 2026-09-08 · fresh-context Codex · neutral brief (artifacts + rubric) · verbatim below.
> Disposition (sa): all 18 accepted; applied in plan v2 (same file, rewritten).
> Key restructure: Task 0 authors ALL scenarios and records baselines BEFORE any
> implementation (fixes 8, 9); R0 no-seat control added (10); deterministic checks
> strengthened with verified predicates skill-not-called / tool-not-called /
> skill-before-implementation-tool (11–14); role-cast + ticket-discipline content made
> faithful to Specs A/C (2, 3, 4); fixture script ordering fixed (5); insertion point
> fixed (6); R6/R7 gate scenarios + 3x runs + pressure-testing added (7); quorum_tier: full
> baked in (15); full scripts repeated per scenario (16); bun run check added (17);
> invariance claim narrowed to non-ticketed standalone (1).

1. **Blocker — Coherence / completeness:** "**Goal:** … while standalone behavior stays byte-for-byte identical" and "no seat named → today's behavior exactly" (lines 5, 7, 17) contradict Task 3's standalone trigger: "Use when working on a ticketed project … in any seat or standalone" (line 198). Spec A explicitly requires ticket discipline in both modes, so standalone ticketed behavior intentionally changes. Resolve by narrowing the invariance claim to non-ticketed standalone sessions, while retaining Spec A's R1 guarantee.

2. **Material — Completeness vs Spec A:** Task 1 says role-cast will contain "exactly this content" (line 45), but its seat table is not verbatim Spec A:
   - Plan: operator "acknowledges artifacts" only implicitly through the artifact rule; Spec A says "acknowledges gates pre-M1."
   - Plan: foreman "acknowledges artifacts"; Spec A qualifies this as "post-M1."
   - Plan omits Spec A's seat-to-holder binding rule requiring a project `PROCESS.md` roles table and treating binding changes as plan changes.
   - Plan omits the explicit gate-receipt rule: acting seat produces the receipt and the foreman/operator countersigns.
   Evidence: Spec A lines 29–40 and 52–53 versus plan lines 60–79. Resolve by making the role-cast content faithfully include every normative row and cross-seat rule from Spec A.

3. **Blocker — Coherence with Spec C:** Ticket-discipline says "`## Scope` and `## Acceptance criteria` are byte-immutable to the builder and foreman seats" (line 211). Spec C makes these sections immutable only to the builder (Spec C line 29); Spec A prohibits the foreman from authoring intent artifacts but does not redefine Spec C's format-level immutability rule. This violates the plan's own "if any line below disagrees … the spec wins" instruction (line 193). Resolve by reproducing Spec C's normative rule exactly and leaving foreman prohibitions to role-cast.

4. **Blocker — Completeness vs Spec C:** Task 3 claims its exact content is "schema is Spec C" (line 193), but omits material normative schema:
   - The complete lifecycle transition table, including `review → in-progress`.
   - The `parked` transition requirement and mandatory `Parked: <reason>` line.
   - The requirement that every review finding be dispositioned before `done`.
   - Spawn-brief grammar beyond the brief mentioned elsewhere.
   - Machine-readable anchors.
   - Documentation-standard requirements, particularly `PROCESS.md`, STATUS/handover, and the roles table.
   Evidence: Spec C lines 48–60 and 72–95 versus plan lines 205–232. Since Spec A says ticket-discipline's content is defined by Spec C, this is not an optional summary. Resolve by implementing all normative Spec C content or explicitly delimiting which portions remain authoritatively available elsewhere with no claim that the skill contains the schema.

5. **Blocker — Accuracy of receipts:** Task 5 says the committed-change command is "appended to the fixture script before its final line" (lines 384–389). The copied Task 2 fixture's final line is `git init -q && git add -A && git commit -qm init` (line 157). Inserting `git add hello.txt && git commit` before that line runs Git before repository initialization and fails. Resolve by initializing and committing the base fixture first, then creating and committing `hello.txt`, or provide the complete setup script in executable order.

6. **Material — Accuracy of insertion point:** Task 4 says to insert `## Foreman-Present Plan Review` "immediately after `## Self-Review`" (line 305). In the actual skill, `## Self-Review` begins at line 162 and its body runs through line 172. Literal insertion immediately after the heading would place the new heading before the existing self-review body, reparenting that body under the new section. Resolve by specifying insertion after the complete Self-Review section and before `## Execution Handoff`.

7. **Material — Mandatory testing workflow incomplete:** Spec A requires "Each changed skill" to go through writing-skills' draft, adversarial pressure testing "across sessions," and eval (Spec A lines 111–125); `CLAUDE.md` likewise requires adversarial testing across multiple sessions and before/after evidence (lines 93–100). The plan provides no direct behavioral eval for either modified brainstorming review gate or the new writing-plans review gate, and generally schedules one live attempt per scenario. Resolve by adding pressure scenarios and multiple independent runs covering each changed behavior, including the two foreman-present review gates.

8. **Material — Before/after methodology is invalid for R3:** Task 3 creates `ticket-discipline` before it runs the supposed failing R3 eval (lines 193–235, 283–284), while role-cast already carries the same immutability prohibition. The prescribed "before" run therefore occurs after the behavior-shaping implementation exists and may pass. "Expected: FAIL … no ticket-discipline rules loaded" is factually inconsistent with the task order. Resolve by running and recording the baseline before creating either applicable rule, then running the same scenario after implementation.

9. **Material — Before/after methodology is absent for R4 and R5:** Task 4 edits all three skills before creating/running R5 (lines 299–368), and Task 5 edits requesting-code-review before creating/running R4 (lines 378–427). Task 6 nevertheless proposes a before/after table using "Task-N Step where it failed" (line 437), although neither workflow contains a genuine pre-change run. Resolve by moving baseline runs before implementation and recording actual run IDs/results rather than an assumed failing step.

10. **Material — R1 does not prove its stated regression contract:** The plan says R1 proves "behavior identical" and "zero role content surfaces" (lines 20, 27, 436). The existing R1 deterministic checks only require brainstorming to be called before native Write/Edit; neither the story nor checks prohibits role-cast invocation or role content. Passing R1 therefore does not establish the claimed no-seat isolation. Resolve with an explicit `skill-not-called superpowers:role-cast` check and a judge criterion/check for absence of role-specific behavior, preferably as a paired no-seat scenario.

11. **Material — R2 is gameable:** Its acceptance criteria require role-cast to run before every other skill/action and require correct builder prohibitions (lines 121–129), but `checks.sh` only checks that role-cast was called at some point (lines 167–169). An agent can act first, invoke it later, and never state the prohibitions while passing deterministic checks. The counterfactual "No role content would have surfaced without the seat line" cannot be observed in this single seat-bearing run. Resolve with ordering checks, transcript assertions for the obligations/prohibitions, and a paired no-seat control.

12. **Material — R3's immutability check is vacuous:** The plan claims two `file-contains` assertions prove Scope immutability (lines 267–281). They only prove `hello.txt` remains somewhere in the ticket and `greeting.txt` does not appear anywhere. An agent could rewrite or restructure the protected sections while retaining `hello.txt`, or record the required deviation outside the artifact, and pass. Resolve by comparing the exact protected section bytes before/after and deterministically checking for a Builder Report with a non-`none` deviation.

13. **Material — R4 does not test its core behavior:** R4 requires no reviewer dispatch and routing to the foreman (lines 409–412), while its only post-check is `skill-called superpowers:role-cast` (lines 415–424). An agent that dispatches a reviewer directly passes. Resolve with transcript checks proving no dispatch/reviewer tool call and observable evidence that the request was routed to the foreman with artifact pointers.

14. **Material — R5 does not test its core behavior:** R5's post-check requires role-cast and absence of `hello.txt` (lines 355–365). An SA can invoke SDD, dispatch a builder that does nothing, or simply stall, and pass. Resolve with transcript checks that SDD was not invoked, no builder/subagent dispatch occurred, and the response explicitly escalated to the foreman.

15. **Minor — Plan violates the target skill's own no-placeholder rule:** Scenario frontmatter repeatedly contains `quorum_tier: <common tier per evals/README>` (for example lines 109, 244, 336, 398), while `writing-plans` explicitly classifies placeholders as plan failures and says actual content must be supplied. The repository already exposes valid values (`sentinel`, `full`, `adhoc`), and the cited example uses `sentinel`. Resolve by selecting the intended tier in the plan rather than deferring it.

16. **Minor — Plan violates the target skill's prohibition on "Similar to Task N":** "`setup.sh`: identical fixture to Task 2's `setup.sh`" (line 265) and the same-fixture instructions in Tasks 4–5 conflict with `writing-plans` line 158: "Similar to Task N (repeat the code — the engineer may be reading tasks out of order)." Resolve by including each complete setup script.

17. **Minor — Static verification is incomplete:** The plan repeatedly runs `bun run quorum check`, but the eval README defines `bun run check` as the full Biome/typecheck/test gate and lists both commands as routine PR checks (README lines 436–447). Resolve by adding the full repository gate to the final sweep.

18. **Over-engineering:** No finding. The proposed file set and role-conditioned changes remain within Spec A's stated scope; the principal issues are omissions, contradictions, and inadequate tests rather than unnecessary architecture.
