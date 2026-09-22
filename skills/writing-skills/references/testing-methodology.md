# Testing Methodology for Skills

**REQUIRED:** see [testing-skills-with-subagents.md](../testing-skills-with-subagents.md) for the complete pressure-scenario methodology — how to write scenarios, pressure types (time, sunk cost, authority, exhaustion), plugging holes systematically, meta-testing.

## TDD mapping for skills

| TDD Concept | Skill Creation |
|-------------|----------------|
| **Test case** | Pressure scenario with subagent |
| **Production code** | Skill document (SKILL.md) |
| **Test fails (RED)** | Agent violates rule without skill (baseline) |
| **Test passes (GREEN)** | Agent complies with skill present |
| **Refactor** | Close loopholes while maintaining compliance |
| **Write test first** | Run baseline scenario BEFORE writing skill |
| **Watch it fail** | Document exact rationalizations agent uses |
| **Minimal code** | Write skill addressing those specific violations |
| **Watch it pass** | Verify agent now complies |
| **Refactor cycle** | Find new rationalizations → plug → re-verify |

## RED-GREEN-REFACTOR in detail

**RED — write the failing test (baseline).** Run the pressure scenario with a subagent WITHOUT the skill. Document exact behavior: what choices did they make? What rationalizations did they use (verbatim)? Which pressures triggered violations? You must see what agents naturally do before writing the skill.

**GREEN — write the minimal skill.** Address those specific rationalizations. Don't add extra content for hypothetical cases. Run the same scenarios WITH the skill; the agent should now comply.

**REFACTOR — close loopholes.** Agent found a new rationalization? Add an explicit counter (see `bulletproofing.md`). Re-test until bulletproof.

## Testing by skill type

**Discipline-enforcing skills** (TDD, verification-before-completion): academic questions (do they understand the rules?), pressure scenarios (do they comply under stress?), multiple pressures combined (time + sunk cost + exhaustion). Identify rationalizations and add explicit counters. Success = agent follows the rule under maximum pressure.

**Technique skills** (condition-based-waiting, root-cause-tracing): application scenarios (can they apply it correctly?), variation scenarios (edge cases?), missing-information tests (do instructions have gaps?). Success = agent applies the technique to a new scenario.

**Pattern skills** (mental models): recognition scenarios (do they see when it applies?), application scenarios, counter-examples (do they know when NOT to apply?). Success = correct identification of when/how to apply.

**Reference skills** (API docs, command references): retrieval scenarios (can they find the right information?), application scenarios (can they use what they found?), gap testing (are common use cases covered?). Success = agent finds and correctly applies the information.

## Micro-test wording before full scenarios

Full pressure-scenario runs are the final gate, but they are slow and expensive per iteration. Verify the wording itself first:

1. **One fresh-context sample per call** — a raw API call, or a single-shot subagent if you don't have API access. System prompt = the realistic context the guidance will live in (the full skill or prompt template, not the guidance in isolation); user message = a task that tempts the failure.
2. **Always include a no-guidance control.** If the control doesn't exhibit the failure, there is nothing to fix — stop, don't author the guidance.
3. **5+ reps per variant.** Single samples lie.
4. **Manually read every flagged match.** Template echoes and quoted counter-examples masquerade as hits; automated counts alone overstate both failure and success.
5. **Variance is a metric.** When guidance lands, reps converge on the same shape. Five different interpretations across five reps means the wording isn't binding — tighten the form before adding words.

Micro-tests verify wording; they do not replace pressure scenarios for discipline skills.

## Rationalizations for skipping testing

| Excuse | Reality |
|--------|---------|
| "Skill is obviously clear" | Clear to you ≠ clear to other agents. Test it. |
| "It's just a reference" | References can have gaps, unclear sections. Test retrieval. |
| "Testing is overkill" | Untested skills have issues. Always. 15 min testing saves hours. |
| "I'll test if problems emerge" | Problems = agents can't use skill. Test BEFORE deploying. |
| "Too tedious to test" | Testing is less tedious than debugging bad skill in production. |
| "I'm confident it's good" | Overconfidence guarantees issues. Test anyway. |
| "Academic review is enough" | Reading ≠ using. Test application scenarios. |
| "No time to test" | Deploying untested skill wastes more time fixing it later. |

**All of these mean: test before deploying. No exceptions.**
