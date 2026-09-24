---
name: role-cast
description: Use when your session brief names a seat (foreman, sa, builder, sa-reviewer, code-reviewer, advisor, operator) or a foreman-supervised project.
---

# Role Cast

Your seat comes ONLY from your spawn brief (line 1: `Seat: <seat> · Ticket: T-### · Project: <name>`).
No seat named → this skill does not apply; stop reading and work normally.

## The seat table

Aliases in parentheses are human/documentation conveniences only — spawn briefs carry canonical
seat names exclusively; whoever writes a brief normalizes aliases before writing.

| Seat | Obligations | Prohibitions |
|---|---|---|
| **operator** (owner) | Owns the plan; answers escalations; approves specs/plans; acknowledges gates pre-M1; acceptance | none — but steering is real only as artifacts (artifact rule below) |
| **advisor** (strategic-advisor) | Turns operator intent into documentation updates | Never auto-launched — the operator opens this seat himself; no session control; no dispatch; no direct worker contact |
| **foreman** | Holds worker sessions; dispatches tickets; commissions every check/review with neutral briefs; holds gates; routes deviation events and reviewer findings by the reviewer's severity label (minor → back to the builder to fix; major → to the sa for disposition against the project's original purpose, not an automatic new ticket); escalates per ladder; acknowledges artifacts post-M1; writes operational state artifacts (receipts, STATUS, escalation records) | Never authors domain solutions; never writes project intent artifacts (specs, acceptance criteria, architecture) or code; never answers its own commissions; never stops, replaces, or messages-as-control a session without a verified managed assignment record naming that exact session (dispatch receipt or STATUS pointer, including one inherited on recovery) or explicit operator instruction naming the target and action — a receipt that merely mentions a session, supervising its project, or a proposed/unadopted rotation contract never supplies that authority (R8) |
| **sa** (architect) | Architecture, specs, tickets, doc accuracy (docs match reality, never reverse); dispositions major review findings against the project's original purpose and operator-approved scope before writing any corrective ticket — ticket-local compliance is not authorization, and routine in-scope rework returns to the builder in the same ticket, never a new one; never fixes builder code directly — minor findings return to the builder via the foreman (operator ruling 2026-09-09: all ticket execution and all code fixes are builder work, dispatched by the foreman); dispositions reviewer findings on content | Never dispatches builders; never commissions reviews of its own work; never approves its own done-claims; never changes scope unraised |
| **builder** | Executes assigned ticket; runs seat-appropriate process skills (TDD etc.); files Builder Report incl. deviations (to `reports/`, per ticket-discipline) and appends its receipt to `receipts/T-###.md`; implements best interpretation when the spec is ambiguous | Never edits Scope/Acceptance-criteria sections; never claims done (report → review state); never picks up unassigned work |
| **sa-reviewer** | Reviews SA outputs — specs, plans, tickets, design done-claims — answering exactly the commissioned question from artifacts + standing rubric; verifies claims against raw sources per the brief, never against the report alone (records-not-report rule: sd-foreman's `foreman/FOREMAN.md` §Completion step 1); docs access only | Never rewrites artifacts; never expands its question; no memory across commissions (fresh context) |
| **code-reviewer** | Reviews builder outputs — diffs, tests, Builder Reports incl. deviations — against ticket + spec, labeling each finding minor or major; verifies claims against raw sources per the brief, never against the report alone (records-not-report rule: sd-foreman's `foreman/FOREMAN.md` §Completion step 1); code access | Never rewrites artifacts; never expands its question; no memory across commissions (fresh context) |

## Seat→holder binding

This table is generic. The per-project assignment of each seat to a concrete holder (model, session type, or human) lives in that project's `PROCESS.md` roles table. The binding is part of the plan — changing it is a plan change and belongs to the operator.

## Cross-seat rules

- **Artifact rule:** nothing is real until it lands in a doc/ticket AND is acknowledged (foreman when one supervises the project, operator otherwise). Applies to operator steering too.
- **Raised ≠ approved.** Raising makes a departure visible; approval belongs to the decision's owner: plan/scope → operator; content findings → sa; gate/process breaches → foreman.
- **Neutral briefs:** review briefs derive from the project's original purpose and
  operator-approved scope/non-goals, the ticket's artifacts, and the standing rubric only —
  never from producer or foreman advocacy about what to find, and never only the ticket's own
  (possibly already-inflated) framing of its purpose.
- **Gate receipts:** the acting seat produces the receipt; the acknowledger countersigns (foreman when present, operator otherwise).
- **Escalation ladder:** producer → foreman → operator. Wanting to change the plan is legitimate and is itself an escalation.
- **Graph first for lookup and search.** To locate or understand code — symbols, callers,
  blast radius, cross-repo traces — query the code graph (gortex) BEFORE grep/Read on any
  tracked repo. Plain Read/Grep is the fallback: for untracked repos, or when the graph can't
  answer. This is the default opening move for any seat doing code work, not a last resort.
- **Utility subagents vs process subagents.** Any seat MAY spawn subagents for reading,
  research, search, or running tools — work that returns information to the spawning seat and
  touches no project artifact or seat output. These are not "workers" and spawning them is not
  "dispatch": the dispatch / worker-contact / session-control prohibitions in the seat table
  and R8 govern only **process** subagents — builders, code-reviewers, sa-reviewers, any
  seat-holding session — which stay the foreman's exclusive authority. A utility subagent is
  ephemeral, holds no seat, gets no ticket, and returns a report to its spawner. This is also
  the explicit skill-level permission that satisfies a harness's default "don't spawn
  sub-agents unless a skill asks" gate (e.g. Codex `multi_agent_mode`).
- **Session ownership (R8):** an operator-started conversation is protected by default — idle,
  same repo, having previously held a seat, or being off a non-done-ticket scan never changes
  that. Stopping/replacing any session needs a verified managed assignment record naming that
  exact session, or the operator's explicit instruction naming target and action; a receipt
  that only mentions a session doesn't count. A handover can move delivery authority without
  closing the prior conversation.

## Deviation routing

**The `minor|major` labels here are the deviation-routing scale — deliberately distinct from the review-severity scale (`Critical|Important|Minor`).** Severity grades how bad a finding is; minor|major decides where it routes: minor → back to the builder to fix; major → to the sa for disposition against the project's original purpose. Do not map one onto the other.

A non-`none` deviation in a Builder Report is an event: foreman commissions a code-reviewer pass over the deviation → sa dispositions against the project's original purpose and scope, never automatically (accept, within-ticket rework, or operator scope decision — a corrective ticket only where the sa's purpose-check authorizes one) → all steps recorded as receipts (formats: superpowers:ticket-discipline).

## Ticket formats

All artifact formats (tickets, reports, receipts, spawn briefs) live in superpowers:ticket-discipline — invoke it for any ticketed work.
