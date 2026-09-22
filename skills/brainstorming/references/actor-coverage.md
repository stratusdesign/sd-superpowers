# Actor Coverage (mandatory analysis; the table when complexity justifies it)

A design or plan that has not accounted for every material actor, surface, capability constraint,
and success condition is mis-scoped. The ANALYSIS is mandatory for every design; the tabular
format is not.

**Compact actor statement** — for genuinely single-actor, single-surface, low-risk work, one
sentence-pair suffices:

```markdown
**Actor scope:** Developer using the CLI to correct one configuration value. Success means the
existing validation command passes with the intended value.
```

**Full actors table** — required when ANY of these applies: multiple human users · multiple
surfaces or runtimes · humans plus agents/automations · meaningful handoffs between systems ·
materially different capabilities · different success conditions · cross-platform workflows ·
agent-facing APIs, tools, or protocols · uncertainty about who actually consumes the result.

```markdown
| Actor | Surface/runtime | Job to be done | Capability constraints | Success test |
|---|---|---|---|---|
```

- **Actor:** human, agent, automation, external system, or operational role.
- **Surface/runtime:** browser, mobile app, shell, server process, scheduled job, API client, or other execution environment.
- **Job to be done:** what the actor is actually trying to accomplish.
- **Capability constraints:** limits that materially affect the design — e.g. cannot run shell commands, move files, retain state, access credentials, or receive interactive input.
- **Success test:** the observable condition under which THIS actor calls the outcome successful.

Rules (they apply to the compact statement AND the table — the format changes, the discipline doesn't):
- Draft actor coverage from inspected evidence and the user's statements (repository files, project docs, observed workflows, confirmed external constraints) — never invent actors or requirements. Mark ANY uncertain actor, surface, capability constraint, or success condition `UNCONFIRMED` and resolve material uncertainty before proposing approaches; minor uncertainty may remain visibly marked where it does not prevent a valid design. Uncertainty about who consumes the result always triggers the full table.
- A row per REAL combination — "the user" is never one row if they act from two surfaces with different capabilities (browser vs shell, phone vs desktop).
- Capabilities constrain design: an actor that cannot move bytes, run a shell, or hold state needs a different pipeline, not a footnote. If two rows need two mechanisms, the design says so explicitly — one mechanism that serves only some rows is a mis-scoped design.
- Reviews cannot catch what the scope never contained. This is the frame-check; intelligence spent after a wrong frame only polishes the wrong thing (proven: a heavily-reviewed design once served one platform while the real task crossed two — e.g. browser Claude cannot move files; shell agents can).
