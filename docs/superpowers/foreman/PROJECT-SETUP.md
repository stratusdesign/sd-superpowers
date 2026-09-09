# Setting Up a New Foreman-Supervised Project

> The setup-time definition of a project. Everything here is authored ONCE, at setup, by the
> operator (or an sa on the operator's word) — it is NOT accreted mid-run. Once the project runs,
> the foreman holds it; rulings that change this setup are plan changes and belong to the operator.
> Canon references: methodology-roles spec (Spec A), ticketing/doc standard (Spec C), the
> orchestration-harness spec (Spec B), and the harness repo's FOREMAN.md.

## 0. Before you start — prerequisites

- The Happier daemon is up (systemd user unit) with `IS_SANDBOX=1` on its environment
  (workers run bypass-mode under root — path A). Verify: `systemctl --user cat
  happier-daemon.default.service | grep IS_SANDBOX`.
- `happier` CLI available; the foreman/worker spawn form is the E8-verified
  `happier session create --path <dir> --json`.
- sd-superpowers present (the methodology this project inherits).

## 1. GitHub repo — right account, matched credential

**The operator's canonical account is `stratusdesign`.** Create the repo there unless the
operator says otherwise.

This machine stores MULTIPLE github credentials (`stratusdesign`, `pixelwebweaver`,
`x-access-token`). **Match the credential to the account explicitly — never take the
`git credential fill` default** (it returns the wrong account). If you are already pushing to
stratusdesign in this session, just keep using that identity — do not "re-find" a credential.

```
# create under stratusdesign, using the stratusdesign token explicitly
SDTOKEN=$(git credential fill <<< $'protocol=https\nhost=github.com\nusername=stratusdesign\n' | sed -n 's/^password=//p')
curl -s -H "Authorization: token $SDTOKEN" https://api.github.com/user/repos \
  -d '{"name":"<project>","private":false,"description":"<one line>"}'
git remote add origin https://stratusdesign@github.com/stratusdesign/<project>.git
```

Repo creation is outward-facing: confirm the returned `full_name` is `stratusdesign/<project>`
before pushing. Wrong-account repos are hard to clean up (delete scope is often absent).

## 2. Scaffold the project files (Spec C doc standard)

Minimum set — nothing listed before it exists:

```
<project>/
  README.md            what it is; what it depends on; that dependencies stay standalone
  PROCESS.md           roles table + bindings + launch rules (see §3)
  backlog.md           index table: | ID | Title | Deliverable | Status |
  tickets/             T-###-<slug>.md per Spec C
  docs/STATUS.md       infra/handover state — the authoritative "where things are"
```

A project that will be foreman-run also carries `foreman/FOREMAN.md` (the harness's operating
procedure) — copy it from the harness repo, or inherit the methodology default.

## 3. PROCESS.md — the setup definition (operator-authored)

The roles table binds each active seat to a concrete holder (model/session type/human). The
operator sets these; changing a binding later is a plan change.

```
## Roles
| Seat | Held by |
|---|---|
| operator | the human |
| sa | <model/session> |
| foreman | <model — ideally a different family from the sa> |
| builder | <model> |
| code-reviewer | <model> |
| sa-reviewer | <model, or bound per dispatch> |

## Session launch
- Every seat session (builder, reviewer, sa) launches as a Happier daemon session
  (E8 spawn form), model per the roles table. Happier's management layer is the point —
  local CLI tools are NOT used to run seat work.
- The sa is ONE persistent session: the foreman spawns it once as a full Happier session under
  the project path (never a subagent run) and reuses it for every content disposition; it is
  the one session exempt from per-task teardown. (Operator ruling A, 2026-09-09.)
- Builders and reviewers are ephemeral: one job, torn down at signoff, teardown verified
  (`active:false` in the session list — a stopped session stays listed; absence is never the
  check). Every spawned session — including throwaway backend/flag-discovery probes — gets a
  dispatch receipt and a teardown-verification receipt.
- Subagent / execution runs (Happier feature) are NOT assigned seat work unless the operator
  rules so.
```

## 4. First tickets

Generate the project's first tickets from the approved plan (Spec C schema). The dogfood/first
work item should be a real, builder-owned deliverable OUTSIDE the foreman's write boundary
(never STATUS/backlog/receipts, which the foreman itself writes).

## 5. Spawn the foreman, then hands off

```
happier session create --path <project-abs-path> --model <foreman-model> --permission-mode bypassPermissions --json
# then send, first line exactly:
#   Seat: foreman · Ticket: T-### · Project: <name>
#   Boot per foreman/FOREMAN.md.
```

After the boot brief, do not drive it. Watch its transcript; answer only what it escalates.
The operator answers plan/scope escalations and the questions the foreman raises; content
findings route to the sa. Everything real is a committed artifact + acknowledgment (the
artifact rule) — direct chat authorizes nothing until it lands.

## What is operator-only (never the foreman's or sa's to decide)

- The GitHub repo and its account; seat→holder bindings; any plan/scope/spec change;
  accepting a milestone. These are set at setup or raised as escalations — not accreted into
  PROCESS.md mid-run by a worker.
