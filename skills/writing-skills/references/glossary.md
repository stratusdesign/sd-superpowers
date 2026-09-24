# Decision Vocabulary Glossary

Canonical terms for decisions that recur across skills. Write new skills in this vocabulary; do not fork it. Two scales that look similar but decide different things are listed as deliberately distinct — keep them apart.

| Term | Meaning | Where used |
|---|---|---|
| **Critical / Important / Minor** | Review-finding severity, all independent reviews: Critical = must fix before the gate passes; Important = must be fixed or explicitly adjudicated; Minor = deferred (ledger/notes), never blocks or triggers another review pass | requesting-code-review, subagent-driven-development (task/re-/final review), brainstorming (design + spec review gates) |
| **minor / major** | Deviation-ROUTING scale, deliberately distinct from severity: decides who dispositions (minor → builder fixes in-ticket; major → sa purpose-check). Never map onto Critical/Important/Minor | role-cast (deviation routing, reviewer seats), ticket-discipline receipts |
| **VERIFIED / INFERRED / UNSUPPORTED** | Classification of a factual CLAIM against evidence (repo, docs, observed output) | reviewer prompts (design, spec, code, task), review-gates synthesis |
| **evidence-backed / unsupported / invented / preference-only** | Adjudication of a FINDING: apply evidence-backed findings; reject unsupported, invented, or preference-only ones, with reasoning. Distinct from claim classification above | requesting-code-review, SDD final review, brainstorming review-gates |
| **material** | The relevance test: would it change actor coverage, scope, constraints, success conditions, architecture, security, or failure handling? Non-material = wording, style, preference, speculation | brainstorming (questions, disagreement, revisions), review gates |
| **complete** | The work-completion gate — claimed only with fresh verification evidence (verification-before-completion). Distinct from `done`: the terminal ticket STATUS in ticket-discipline's wire format | verification-before-completion, SDD ledger lines, executing-plans |
| **DONE / DONE_WITH_CONCERNS / NEEDS_CONTEXT / BLOCKED** | Implementer report statuses — a dispatch wire contract, not prose | SDD implementer contract |
| **dispatch vs spawn** | Dispatch = handing a ticket/task to a process subagent or seat (foreman authority under role-cast); spawning a utility subagent (read/search/tool work, no seat, no artifact) is not dispatch | role-cast, SDD |
| **park** | Deliberate deferral with a recorded ruling — a ledger/receipt entry, never a silent discard | SDD fix-loop breaker, ticket-discipline status |
| **adjudicate vs disposition** | Currently distinct: adjudicate = the SDD controller's own ruling on open findings (at the fix-loop cap or final review); disposition = a seat's ruling routed through the foreman machinery (sa on major findings/deviations). Flagged for possible unification — an operator call, since it touches seat authority | SDD; role-cast, ticket-discipline |
