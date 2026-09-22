---
name: using-superpowers
description: Use when starting any conversation - establishes how to find and use skills, requiring skill invocation before ANY response including clarifying questions
---

<SUBAGENT-STOP>
If you were dispatched as a subagent to execute a specific task, ignore this skill.
</SUBAGENT-STOP>

## The Rule

**Check for applicable skills BEFORE any response or action** — including clarifying questions, exploring the codebase, or checking files. The check is the reflex; loading is judgment:

- **Load a skill when it plausibly governs the task in front of you.** Not "might conceivably relate" — plausibly governs what you are about to do.
- **Process skills load when their situation has actually arrived.** Brainstorming loads when creative or feature work is in front of you — building, adding, or changing behavior. Systematic-debugging loads when a bug, failing test, or unexpected behavior is in front of you. Neither loads at session start, and neither loads because the session might eventually get there.
- **A skill named by the user, or by a project's CLAUDE.md, ALWAYS loads.** This is absolute — no relevance judgment applies.

**Before entering plan mode:** if you haven't already brainstormed, invoke the brainstorming skill first.

Then announce "Using [skill] to [purpose]" and follow the skill exactly. If it has a checklist, create a todo per item. If a loaded skill turns out wrong for the situation, you don't have to use it.

## Skill Priority

When multiple skills apply, process skills come first — they set the approach, then implementation skills (frontend-design, etc.) carry it out. Brainstorming and systematic-debugging are Superpowers' most common process skills, but the rule holds for any of them.

If your session brief names a seat, superpowers:role-cast is the first skill you invoke.

- "Let's build X" → superpowers:brainstorming first, then implementation skills.
- "Fix this bug" → superpowers:systematic-debugging first, then domain skills.

## Red Flags

These thoughts mean STOP—you're rationalizing:

| Thought | Reality |
|---------|---------|
| "I need more context first" | Skill check comes BEFORE clarifying questions. |
| "Let me explore the codebase first" | Skills tell you HOW to explore. Check first. |
| "This doesn't need a formal skill" | If a skill governs this task, use it. |
| "I remember this skill" | Skills evolve. Read current version. |
| "The skill is overkill" | If its situation is in front of you, use it. |
| "I'll just do this one thing first" | Check BEFORE doing anything. |
| "I know what that means" | Knowing the concept ≠ using the skill. Invoke it. |

The inverse rationalization is also a red flag: "the session will probably need brainstorming later, load it now." Load when the work arrives, not in anticipation.

## Platform Adaptation

If your harness appears here, read its reference file for special instructions:

- Codex: `references/codex-tools.md`
- Pi: `references/pi-tools.md`
- Antigravity: `references/antigravity-tools.md`

## User Instructions

User instructions (CLAUDE.md, AGENTS.md, GEMINI.md, etc, direct requests) take precedence over skills, which in turn override default behavior. Only skip skill workflows or instructions when your human partner has explicitly told you to.
