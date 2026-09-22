---
name: brainstorming
description: "You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation."
---

# Brainstorming Ideas Into Designs

Help turn ideas into fully formed designs and specs through natural collaborative dialogue: understand the current project context, ask focused questions to refine the idea, then present the design and get user approval.

<HARD-GATE>
Do NOT invoke any implementation skill, write any code, scaffold any project, or take any implementation action until you have presented a design and the user has approved it. This applies to EVERY project regardless of perceived simplicity.
</HARD-GATE>

**Anti-pattern: "this is too simple to need a design."** Every project goes through this process — a todo list, a single-function utility, a config change. "Simple" projects are where unexamined assumptions cause the most wasted work. The design can be a few sentences, but you MUST present it and get approval.

## Checklist

You MUST create a task for each of these items and complete them in order:

1. **Explore project context** — files, docs, recent commits. If the request spans multiple independent subsystems, help the user decompose first and brainstorm one sub-project at a time.
2. **Establish actor coverage** — before any design question: who uses this, from where, doing what, and what does success look like for each actor? Read `references/actor-coverage.md` when doing this step.
3. **Decide independent co-design (value-triggered)** — runs only when BOTH hold: an independent model route is available AND a material open design decision would benefit from a second perspective. Read `references/independent-co-design.md` when both hold; otherwise skip, and revisit if discovery exposes a material decision.
4. **Offer the visual companion just-in-time** — NOT upfront. Read `references/visual-companion-offer.md` the first time a question would genuinely be clearer shown than described; if no visual question ever arises, never offer it.
5. **Ask clarifying questions** — a single question, a small thematic batch, or a concise list; understand purpose, constraints, success criteria. Keep questions open and concrete — never embed your preferred solution, never restrict the user to your options. Use a single follow-up when one answer determines what to ask next.
6. **Propose 2-3 approaches** — with trade-offs; lead with your recommendation and why. YAGNI ruthlessly.
7. **Present candidate design** — in sections scaled to their complexity; validate each section with the user. Cover architecture, components, data flow, error handling, testing.
8. **Review and synthesize design** — constructive + adversarial review, synthesize against evidence and user intent, then get final user approval. Read `references/review-gates.md` when the complete candidate design is coherent. (Foreman-present: if your brief names a seat, the review is commissioned by the foreman — submit your artifacts to it and do not choose the reviewer or write the review brief.)
9. **Write design doc** — save to `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md` (user preferences for spec location override this default).
10. **Spec self-review** — inline check for placeholders, contradictions, ambiguity, scope; fix inline and move on.
11. **Review and synthesize written spec** — independent adversarial/completeness review, verify and apply findings, establish the canonical spec, commit it. Read `references/review-gates.md` when the spec is written. (Same foreman rule as step 8.)
12. **User reviews written spec** — ask the user to review the canonical spec before proceeding; wait. If they request changes, make them and redo steps 10-11.
13. **Transition to implementation** — invoke the writing-plans skill.

**The terminal state is invoking writing-plans.** Do NOT invoke frontend-design, mcp-builder, or any other implementation skill. The ONLY skill you invoke after brainstorming is writing-plans.

## Governing Rules

- **Simplicity is the default.** Select the simplest complete solution; every added component, abstraction, dependency, or requirement carries the burden of proof. Resolve findings subtractive-first: Delete → Narrow → Simplify → Reuse → Clarify → Add only what remains necessary. Full rules in `references/review-gates.md`.
- **Design for isolation and clarity** — small units, one clear purpose each, well-defined interfaces. In existing codebases, follow existing patterns; include only targeted improvements that serve the current goal.

## Reference Map

Read each file at the moment named, not before:

- Read `references/actor-coverage.md` when establishing actor coverage (step 2) — compact statement vs full actors table, and the rules for both.
- Read `references/independent-co-design.md` when step 3's trigger holds — roles, blind question/approach generation, neutral brief, synthesis, degradation.
- Read `references/design-guidance.md` when starting discovery on non-trivial work (steps 5-7) — question batching, decomposition, approach exploration, design presentation, isolation, existing codebases.
- Read `references/review-gates.md` when running the design review gate (step 8) or the spec reviews (steps 10-11) — simplicity/defect-handling rules, reviewer dispatch, synthesis, user review gate.
- Read `references/process-flow.md` when unsure how a revision, failed gate, or user-requested change re-enters the process — the full decision graph.
- Read `references/visual-companion-offer.md` when a question is first clearer shown than told (step 4); then `visual-companion.md` if the user accepts.
