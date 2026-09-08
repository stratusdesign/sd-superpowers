# Plan A — Codex targeted re-review (round 2)

> 2026-09-08 · fresh-context Codex · scope: adequacy of v2's resolutions + regressions ·
> verbatim below. Disposition (sa): all partial/unresolved items + new findings applied in
> plan v3 — aliases restored to the seat table; ticket-discipline aligned to Spec C
> (PROCESS.md-or-inherit, alias wire rule, optional Receipts, doc paths); R4/R6/R7 now assert
> the edited gate skill was invoked; R2 gains a Bash-ordering check; R3 uses a whole-file
> prefix-diff against a committed baseline; every scenario embeds its full setup script;
> self-review statement corrected.

The v2 rewrite substantially improves the plan, but it does not fully resolve all 18 findings. Blockers 1, 3, and 5 are resolved; blocker 4 remains partial. Several eval findings also remain partial because acceptance criteria rely on the judge without proving the relevant skill was invoked.

## Round-1 findings

1. **Resolved.** The invariance claim is correctly narrowed ("while non-ticketed standalone behavior stays byte-for-byte identical (ticketed projects gain ticket-discipline in both modes…)", plan lines 6–8).

2. **Partial.** All specifically identified normative rules — pre/post-M1 acknowledgment, seat binding, and gate receipts — are now present (plan lines 451–470). However, the plan claims the table reproduces Spec A "verbatim," while it does not: aliases such as `(owner)`, `(architect)`, and `(strategic-advisor)` are omitted, and several cells are paraphrased. The normative substance is mostly faithful, but the explicit verbatim requirement in Spec A §File-by-file is not met.

3. **Resolved.** Immutability is now builder-only (plan line 533). Foreman intent-authoring prohibitions remain separately in role-cast.

4. **Partial — blocker remains.** The lifecycle, parked reason, finding disposition, spawn grammar, anchors, roles table, and STATUS/handover requirements were added (plan lines 521–573). But the proposed skill still does not contain every normative Spec C rule despite claiming "every rule below is restated from it". Missing or altered items include: Spec C's documentation paths for specs and plans; the allowance that a project may inherit the methodology default instead of carrying its own PROCESS.md; the explicit rule that aliases are invalid wire values and brief writers normalize them; the explicit optionality of `## Receipts`. Most importantly, v2 changes Spec C's "`PROCESS.md` (or inherits the methodology default)" into "Every ticketed project carries … `PROCESS.md`" — a stricter, contradictory schema. Either reproduce the full normative portion accurately or remove the "every rule"/full-schema claim and define the intended subset.

5. **Resolved.** The R4 setup now initializes and commits the base fixture before creating and committing hello.txt (plan lines 207–236).

6. **Resolved.** The insertion point is unambiguous ("after the COMPLETE `## Self-Review` section … immediately before `## Execution Handoff`", plan lines 592–598).

7. **Partial.** R6/R7 and three-run pressure testing were added, and Task 6 covers all seven changed/new skills ("Full sweep, three independent runs each … R0–R7 × 3", plan lines 633–641). However, the new gate scenarios do not deterministically require invocation of the skills they purport to evaluate: R4 does not assert superpowers:requesting-code-review was called; R6 does not assert superpowers:writing-plans was called; R7 does not assert superpowers:brainstorming was called. Consequently, role-cast alone could produce the expected routing response while the modifications to those three existing skills remain untested. Add the corresponding skill-called checks or otherwise establish invocation in the transcript criteria.

8. **Resolved.** All scenarios and their baselines are scheduled before implementation (Task 0 precedes Tasks 1–5).

9. **Resolved.** R4 and R5 are now authored and run in Task 0, before the corresponding skill edits. Actual baseline run IDs are explicitly recorded for Task 6.

10. **Resolved.** The new R0 no-seat control includes both judge-visible absence criteria and `check-transcript skill-not-called superpowers:role-cast` (plan lines 82–116). R1 remains the non-ticketed standalone regression.

11. **Partial.** R2 now checks role-cast before implementation Write and Edit, and the story asks for builder prohibitions (plan lines 140–159). This still does not prove Spec A's stronger requirement that role-cast be the first skill invoked — before any other process skill. The predicates also pass vacuously if no implementation Write/Edit occurs. The prohibition answer is judge-carried rather than deterministic. The paired R0 control improves counterfactual coverage, but the ordering hole remains.

12. **Unresolved.** The R3 checks still do not compare exact protected-section bytes. They repeat the same substring strategy rejected in round 1 (plan lines 190–204). An agent can alter other bytes in either protected section and pass. Capture the exact two sections — or their hashes — in setup and compare them in post(). The non-`none` deviation check is improved and is no longer vacuous.

13. **Resolved.** R4 now prohibits the dispatch tool and gives the judge observable routing criteria (`check-transcript tool-not-called Task`; "routes the request to the foreman, supplying only artifact pointers", plan lines 256–273). tool-not-called is a valid predicate.

14. **Resolved.** R5 now checks all requested negative behaviors (skill-not-called superpowers:subagent-driven-development; tool-not-called Task; not file-exists 'hello.txt'). The judge criterion additionally requires explicit escalation (plan lines 296–315).

15. **Resolved.** Every shown scenario uses `quorum_tier: full`.

16. **Unresolved.** The complete base script appears only once. Four scenario steps still say "`setup.sh` = base fixture." (plan lines 118, 162, 276, 384). That remains the same cross-task shorthand prohibited by writing-plans; the self-review claim that "every scenario carries full scripts" is false.

17. **Resolved.** Both static gates are now required globally and in Task 6 (`bun run quorum check` AND `bun run check`, plan line 640).

18. **Resolved / no action required.** Round 1 contained no over-engineering finding, and v2 does not add unnecessary architecture.

## New findings

- **Material — Spec C contradiction:** Task 3 requires every ticketed project to carry PROCESS.md, removing Spec C's permitted "or inherits the methodology default" alternative. This is part of unresolved finding 4, but it is also a new contradiction introduced by the rewrite.

- **Material — Gate evals do not prove the edited gate skills loaded:** R4, R6, and R7 can pass their behavioral judge without invoking requesting-code-review, writing-plans, or brainstorming, respectively. This prevents the plan from demonstrating that those particular modifications caused the behavior.

- **Minor — False self-review statement:** The plan says "every scenario carries full scripts" while four scenarios reference the shared fixture instead of repeating it (plan lines 644–648).

No invalid predicate names were found: skill-not-called, tool-not-called, and skill-before-implementation-tool all exist. The R4 committed-change fixture ordering is now executable.
