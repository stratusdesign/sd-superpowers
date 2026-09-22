# Skill Structure and Discovery Optimization

## Directory structure

```
skills/
  skill-name/
    SKILL.md              # Main reference (required)
    supporting-file.*     # Only if needed
```

**Flat namespace** — all skills in one searchable namespace.

**Separate files for:**
1. **Heavy reference** (100+ lines) — API docs, comprehensive syntax
2. **Reusable tools** — scripts, utilities, templates

**Keep inline:** principles and concepts, code patterns (< 50 lines), everything else.

### File organization patterns

- **Self-contained skill** (`defense-in-depth/SKILL.md`): everything inline. When all content fits and no heavy reference is needed.
- **Skill with reusable tool** (`condition-based-waiting/` + `example.ts`): working helpers to adapt. When the tool is reusable code, not just narrative.
- **Skill with heavy reference** (`pptx/` + API references + `scripts/`): overview and workflows inline, reference material in separate files. When reference material is too large for inline.

## SKILL.md structure

**Frontmatter (YAML):**
- Two required fields: `name` and `description` (see [agentskills.io/specification](https://agentskills.io/specification) for all supported fields)
- Max 1024 characters total
- `name`: letters, numbers, and hyphens only (no parentheses, special chars)
- `description`: third-person, describes ONLY when to use (NOT what it does)

```markdown
---
name: Skill-Name-With-Hyphens
description: Use when [specific triggering conditions and symptoms]
---

# Skill Name

## Overview
What is this? Core principle in 1-2 sentences.

## When to Use
[Small inline flowchart IF decision non-obvious]
Bullet list with SYMPTOMS and use cases. When NOT to use.

## Core Pattern (for techniques/patterns)
Before/after code comparison

## Quick Reference
Table or bullets for scanning common operations

## Implementation
Inline code for simple patterns; link to file for heavy reference or reusable tools

## Common Mistakes
What goes wrong + fixes

## Real-World Impact (optional)
Concrete results
```

## Rich description field

**Purpose:** your agent reads the description to decide which skills to load for a given task. Make it answer: "Should I read this skill right now?"

**Format:** start with "Use when..." to focus on triggering conditions.

**CRITICAL: description = when to use, NOT what the skill does.**

**Why this matters:** testing revealed that when a description summarizes the skill's workflow, an agent may follow the description instead of reading the full skill content. A description saying "code review between tasks" caused an agent to do ONE review, even though the skill's flowchart clearly showed TWO reviews (spec compliance then code quality). When the description was changed to just "Use when executing implementation plans with independent tasks" (no workflow summary), the agent correctly read the flowchart and followed the two-stage review process.

**The trap:** descriptions that summarize workflow create a shortcut agents will take. The skill body becomes documentation agents skip.

```yaml
# ❌ BAD: Summarizes workflow - agents may follow this instead of reading skill
description: Use when executing plans - dispatches subagent per task with code review between tasks

# ❌ BAD: Too much process detail
description: Use for TDD - write test first, watch it fail, write minimal code, refactor

# ✅ GOOD: Just triggering conditions, no workflow summary
description: Use when executing implementation plans with independent tasks in the current session

# ✅ GOOD: Triggering conditions only
description: Use when implementing any feature or bugfix, before writing implementation code
```

**Content:**
- Use concrete triggers, symptoms, and situations that signal this skill applies
- Describe the *problem* (race conditions, inconsistent behavior) not *language-specific symptoms* (setTimeout, sleep)
- Keep triggers technology-agnostic unless the skill itself is technology-specific; if it is, make that explicit
- Write in third person (injected into system prompt)
- Keep under 500 characters if possible

```yaml
# ❌ BAD: Too abstract, vague, doesn't include when to use
description: For async testing

# ❌ BAD: First person
description: I can help you with async tests when they're flaky

# ❌ BAD: Mentions technology but skill isn't specific to it
description: Use when tests use setTimeout/sleep and are flaky

# ✅ GOOD: Starts with "Use when", describes problem, no workflow
description: Use when tests have race conditions, timing dependencies, or pass/fail inconsistently

# ✅ GOOD: Technology-specific skill with explicit trigger
description: Use when using React Router and handling authentication redirects
```

## Keyword coverage

Use words an agent would search for:
- Error messages: "Hook timed out", "ENOTEMPTY", "race condition"
- Symptoms: "flaky", "hanging", "zombie", "pollution"
- Synonyms: "timeout/hang/freeze", "cleanup/teardown/afterEach"
- Tools: actual commands, library names, file types

## Descriptive naming

**Use active voice, verb-first.** Name by what you DO or the core insight; gerunds (-ing) work well for processes:
- ✅ `creating-skills` not `skill-creation`
- ✅ `condition-based-waiting` not `async-test-helpers`
- ✅ `flatten-with-flags` not `data-structure-refactoring`
- ✅ `root-cause-tracing` not `debugging-techniques`

## Token efficiency (critical)

**Problem:** frequently-referenced skills load into many conversations. Every token counts.

**Targets:** getting-started workflows < 150 words each; frequently-loaded skills < 200 words; other skills < 500 words. Verify with `wc -w`.

**Techniques:**
- **Move details to tool help:** reference `--help` instead of documenting all flags.
- **Use cross-references:** don't repeat workflow details another skill owns.
- **Compress examples:** minimal dialogue, no filler turns.
- **Eliminate redundancy:** don't repeat cross-referenced skills, don't explain what's obvious from the command, one example per pattern.

## Cross-referencing other skills

Use the skill name only, with explicit requirement markers:
- ✅ `**REQUIRED SUB-SKILL:** Use superpowers:test-driven-development`
- ✅ `**REQUIRED BACKGROUND:** You MUST understand superpowers:systematic-debugging`
- ❌ `See skills/testing/test-driven-development` (unclear if required)
- ❌ `@skills/testing/test-driven-development/SKILL.md` (force-loads, burns context)

**Why no @ links:** `@` syntax force-loads files immediately, consuming context before you need them.

## Discovery workflow

How future agents find your skill:

1. **Encounters problem** ("tests are flaky")
2. **Searches skills** (greps descriptions, browses categories)
3. **Finds SKILL** (description matches)
4. **Scans overview** (is this relevant?)
5. **Reads patterns** (quick reference table)
6. **Loads example** (only when implementing)

Optimize for this flow — put searchable terms early and often.
