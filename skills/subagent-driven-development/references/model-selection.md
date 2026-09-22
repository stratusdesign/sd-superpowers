# Model Selection

**Roles are model-independent; bindings are concrete.** This section routes two
roles by different criteria:

- **Implementation routing** picks a **standard** or **high-assurance**
  implementer by task risk and suitability — an assurance level, not a model
  family.
- **Review routing** picks a **fresh** reviewer context — never the
  implementer's own — and prefers a **cross-family** reviewer (a different model
  family) where that independence is materially useful.

Harness bindings map those roles onto concrete routes. In Claude Code today the
standard implementer and the same-family reviewer are `general-purpose`, and the
high-assurance implementer is `codex:codex-rescue` — which is also cross-family
relative to the parent Claude session, a property of this binding rather than the
rule. In another harness, bind the same roles to whatever routes exist there — do
not invent a route (e.g. a reverse dispatch) that is not installed and verified.
Route by task suitability, assurance, and review independence, not by model brand
or availability, and do not imply another model family is inherently more
capable.

Route implementation by value and risk first, then choose the least powerful
model that can handle the role. Do not calculate a score.

**High-value or high-risk implementation:** dispatch the high-assurance
implementer route with the same task brief, context, report path, and
implementer contract as any other task. Prefer it when the task involves
architecture changes, authentication or authorization, security-sensitive code,
payments, migrations, data integrity, critical business logic, concurrency,
broad cross-codebase effects, difficult integrations, or failure that would be
expensive. Risk overrides apparent mechanical simplicity: exact plan text or a
one-file diff does not make a payment or authorization change low-risk. For the
mechanics of the Codex binding, see `codex-route.md`.

**Routine or mechanical implementation:** dispatch the standard implementer
route — `general-purpose` with an explicit suitable model. Examples: isolated
changes with a clear spec, straightforward CRUD or UI work, boilerplate, routine
configuration, ordinary tests, mechanical refactoring.

**Neither class clearly dominates:** use the standard route and the judgment
tiers below. Escalate to the high-assurance route only when the actual task has
a high-value/high-risk signal, not merely because that route is available.

If the configured high-assurance implementation capability is unavailable before
dispatch, use the most capable suitable available implementer and disclose the
degraded routing. If the high-assurance route is available but setup,
authentication, dispatch, completion, or result retrieval fails, report the
actionable failure and ask whether to retry or explicitly fall back to the
standard route. Never claim the high-assurance route implemented work when no
usable result exists.

## Model tiers

- **Mechanical implementation tasks** (isolated functions, clear specs, 1-2
  files): fast, cheap model. Most implementation tasks are mechanical when the
  plan is well-specified.
- **Integration and judgment tasks** (multi-file coordination, pattern matching,
  debugging): standard model.
- **Architecture and design tasks**: the most capable available model. The final
  whole-branch review is one of these — dispatch it on the most capable
  available model, not the session default.
- **Review tasks**: same judgment, scaled to the diff's size, complexity, and
  risk. A small mechanical diff does not need the most capable model; a subtle
  concurrency change does. Scoped re-reviews of small fix diffs take a
  cheap-to-mid tier.

**Task complexity signals (implementation tasks):**
- Touches 1-2 files with a complete spec → cheap model
- Touches multiple files with integration concerns → standard model
- Requires design judgment or broad codebase understanding → most capable model

**Always specify the model explicitly when dispatching a `general-purpose`
subagent.** An omitted model inherits your session's model — often the most
capable and most expensive — which silently defeats this section. The
`codex:codex-rescue` route is the explicit exception (its model and effort stay
unset unless the user chose them).

**Turn count beats token price.** Wall-clock and context cost scale with how
many turns a subagent takes, and the cheapest models routinely take 2-3× the
turns on multi-step work — costing more overall. Use a mid-tier model as the
floor for reviewers and for implementers working from prose descriptions. When
the task's plan text contains the complete code to write, the implementation is
transcription plus testing: use the cheapest tier for that implementer.
Single-file mechanical fixes also take the cheapest tier.

## Independent review routing

Record which route implemented each task, then choose a reviewer that is a fresh
context and — where it materially helps — a different model family. In the
current bindings:

- Cross-family (`codex:codex-rescue`) authored work is reviewed by a fresh
  `general-purpose` reviewer with an explicit model tier.
- `general-purpose`-authored work normally uses a fresh `general-purpose`
  reviewer. Route it to the cross-family (`codex:codex-rescue`) reviewer only
  when independent cross-model challenge is materially justified: the work fell
  back from a high-risk high-assurance route, or it relies on uncertain external
  APIs, versions, configuration, repository capabilities, or a difficult
  integration.

Do not route routine same-family work to the cross-family reviewer merely for
symmetry, and do not add a second reviewer or vote between models. Tests do not
replace either review route. A cross-family reviewer must be a different thread
from the implementer that produced the work — never reuse a single
`codex:codex-rescue` thread to implement and then review the same work — receive
the same task-reviewer prompt, and be explicitly told the review is read-only.

If the cross-family review capability is absent, use the most capable available
same-family reviewer and disclose degraded routing. If the cross-family route's
setup, authentication, dispatch, completion, or result retrieval fails, report
the actionable failure and ask whether to retry or explicitly fall back to the
same-family route. Never claim an independent cross-family review when no usable
cross-family result exists.

## Fix-loop escalation (rounds 4-5)

For a standard implementer, use a model at least one tier above the implementer
that got stuck. For a high-assurance (`codex:codex-rescue`) implementer,
dispatch a fresh agent on that route with the existing brief, report, and
findings; leave its model/effort unset unless the user chose them. Fresh context
supplies the escalation without inventing a high-assurance tier.
