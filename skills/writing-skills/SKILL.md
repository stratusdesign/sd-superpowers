---
name: writing-skills
description: Use when creating new skills, editing existing skills, or verifying skills work before deployment
---

# Writing Skills

## Overview

**Writing skills IS Test-Driven Development applied to process documentation.** You write test cases (pressure scenarios with subagents), watch them fail (baseline), write the skill, watch tests pass (agents comply), and refactor (close loopholes).

**Core principle:** if you didn't watch an agent fail without the skill, you don't know if the skill teaches the right thing.

**REQUIRED BACKGROUND:** you MUST understand superpowers:test-driven-development — this skill adapts its RED-GREEN-REFACTOR cycle to documentation.

**Personal skills live in your runtime's skills directory** (`~/.claude/skills/` on Claude Code) — see [codex-tools.md](../using-superpowers/references/codex-tools.md) or [gemini-tools.md](../using-superpowers/references/gemini-tools.md) for other runtimes. Codex, Copilot CLI, and Gemini CLI also recognize `~/.agents/skills/` as a cross-runtime alias.

A **skill** is a reference guide for a proven technique, pattern, or tool — never a narrative about how you solved a problem once. **Create one when** the technique wasn't intuitively obvious, you'd reference it across projects, and it applies broadly. **Don't create one for** one-off solutions, practices well-documented elsewhere, project-specific conventions (instructions file), or mechanical constraints (automate those instead).

**Skill prose follows the house standard:** clear, factual, to the point; no unnecessary jargon; no wasteful sentences — each carries weight; properly formatted English.

## The Iron Law (same as TDD)

```
NO SKILL WITHOUT A FAILING TEST FIRST
```

This applies to NEW skills AND EDITS to existing skills. Write or edit a skill before testing? Delete it. Start over.

**No exceptions:** not for "simple additions", not for "just adding a section", not for "documentation updates". Don't keep untested changes as "reference". Don't "adapt" while running tests. Delete means delete.

## The Cycle

1. **RED** — run the pressure scenario WITHOUT the skill; document baseline behavior and rationalizations verbatim.
2. **GREEN** — write the minimal skill addressing those specific failures; re-run, verify compliance.
3. **REFACTOR** — close each new loophole with an explicit counter; re-test until bulletproof.

**Read `references/testing-methodology.md` before testing any skill** — scenario design by skill type, micro-testing wording, the skip-testing rationalization table. **Read `references/bulletproofing.md` when a discipline skill needs to resist pressure** — loophole closing, rationalization tables, red-flags lists.

## Match the Form to the Failure

Before writing guidance, classify the baseline failure. The form that bulletproofs one failure type measurably backfires on another.

| Baseline failure | Right form | Wrong form |
|---|---|---|
| Skips/violates a rule under pressure (knows better, does it anyway) | Prohibition + rationalization table + red flags | Soft guidance ("prefer...", "consider...") |
| Complies, but output has the wrong shape (bloated prompt, buried verdict, restated spec) | Positive recipe or contract: state what the output IS — its parts, in order | Prohibition list ("don't restate", "never narrate") |
| Omits a required element from something they already produce | Structural: REQUIRED field or slot in the template they fill in | Prose reminders near the template |
| Behavior should depend on a condition | Conditional keyed to an observable predicate ("if the brief exists, reference it") | Unconditional rule + exemption clauses |

Prohibitions backfire on shaping problems; recipes leave nothing to negotiate. Evidence and the no-nuance-clause rules are in `references/bulletproofing.md`.

## Structure and Discovery

Frontmatter carries exactly `name` (letters/numbers/hyphens) and `description` (third person, "Use when..." — triggering conditions ONLY, never a workflow summary: agents follow a summarized description instead of reading the skill). **Read `references/structure-and-discovery.md` before writing a new SKILL.md** — the template, description examples, keyword coverage, naming, token-efficiency targets, and cross-referencing rules (never `@`-links). **Read `references/style-and-antipatterns.md` when adding flowcharts or examples.**

## STOP: Before Moving to Next Skill

After writing ANY skill, STOP and complete deployment: test it, close loopholes, commit. Do NOT batch multiple skills without testing each. Deploying untested skills = deploying untested code.

## Checklist (create a todo per item)

**RED:** pressure scenarios written (3+ combined pressures for discipline skills) · baseline run WITHOUT skill, behavior documented verbatim · failure patterns identified.

**GREEN:** valid frontmatter (name, description ≤1024 chars, "Use when...", third person) · keywords for search · clear overview with core principle · addresses the specific baseline failures · guidance form matches the failure type · behavior-shaping wording micro-tested against a no-guidance control (5+ reps, read every flagged match; N/A for pure reference) · one excellent example · scenarios re-run WITH skill, compliance verified.

**REFACTOR:** new rationalizations countered · rationalization table built · red-flags list created · re-tested until bulletproof.

**Quality:** flowchart only if the decision is non-obvious · quick-reference table · common-mistakes section · no narrative storytelling · supporting files only for tools or heavy reference.

**Deploy:** commit and push · consider contributing back via PR.

## Reference Map

- Read `references/testing-methodology.md` before testing any skill; [testing-skills-with-subagents.md](testing-skills-with-subagents.md) holds the full pressure-scenario methodology.
- Read `references/bulletproofing.md` when hardening a discipline skill; [persuasion-principles.md](persuasion-principles.md) holds the research foundation.
- Read `references/structure-and-discovery.md` before writing a new SKILL.md or its description.
- Read `references/style-and-antipatterns.md` when adding flowcharts or code examples.
- Read [anthropic-best-practices.md](anthropic-best-practices.md) for Anthropic's official authoring guidance, which complements this skill's TDD approach.
