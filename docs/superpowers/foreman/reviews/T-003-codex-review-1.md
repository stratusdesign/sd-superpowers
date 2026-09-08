# T-003 / Spec B — Codex adversarial review, round 1

> 2026-09-08 · fresh-context Codex · neutral brief (artifacts + standing rubric) · verbatim.
> Disposition: all 10 accepted by sa; applied in spec draft v2.

1. **Blocker — Check moment 3 has no event source and therefore cannot satisfy T-003's matrix criterion.**

   **Passage:** "`NO pre-action source in v1` … `Raised → escalate to operator; worker holds because the foreman withholds the go-ahead`" (Spec B:79).

   **Evidence:** T-003 requires each event to have a source (T-003:16). The concept permits layer 1 to be absent, but then names "spawn scoping + escalation obligation" as the alternative (concept:194). Neither observes an unraised irreversible action: scoping merely removes some capabilities, while the obligation depends on voluntary disclosure. Hermes is post-hoc and its request channel is itself unbuilt. Thus the Goal's claim that the foreman "observes the four check moments" is also false (Spec B:11).

   **Resolution:** State explicitly that unraised check-moment-3 events are unobservable in v1, define the precise observable raised-event source, and narrow the Goal and acceptance-status claim accordingly. If observation of every instance is required, add a real source and its experiment.

2. **Blocker — "Worker blocks" and fail-closed behavior are asserted without a runtime stop.**

   **Passages:** "`blocking = no answer, not a mechanical stop`" (Spec B:79); "`Dead foreman / broken MCP / lost connection … gated work stops. No degraded ungated mode exists`" (Spec B:102); "`escalation blocks the worker until answered`" (Spec B:169).

   **Evidence:** Workers deliberately run in bypass mode with no pre-action interception (concept:347–362). Withholding a reply prevents a formal transition or approval, but it does not prevent an already-running worker from continuing work or performing an available outside-world action. The spec itself concedes that there is no mechanical stop. The construction proves that foreman-owned artifact transitions stop, not that worker execution stops.

   **Resolution:** Distinguish "gate/ticket progression blocks" from "worker execution blocks," define what mechanically stops or suspends a live session if that is required, and attach an experiment to the stop mechanism. Otherwise narrow all fail-closed and worker-blocking claims to the artifact gate actually enforced.

3. **Material — The escalation fallback contradicts the admitted absence of a foreman→Hermes channel.**

   **Passages:** "`Fallback: Hermes → Telegram`" (Spec B:88); "`Session list unavailable … escalate via fallback transport`" (Spec B:77); but "`The foreman→Hermes request channel is unbuilt … E6`" (Spec B:128).

   **Evidence:** A live Hermes→Telegram path does not establish that the foreman can invoke it. The spec explicitly says it cannot yet do so. Therefore the stated failure behavior is unavailable precisely when the primary session-control path fails. Labeling the downstream notify path `DECLARED` does not establish the missing upstream transport.

   **Resolution:** Mark the fallback as operator/Hermes-initiated until E6, specify the actual trigger available today, and stop claiming automatic fallback escalation before E6 passes.

4. **Material — Check moment 1 observes session identity, not work starting, and is readily gameable.**

   **Passage:** "`workers exist only by foreman dispatch`" and "`account session list diffed against in-progress tickets`" (Spec B:77).

   **Evidence:** The event is "work starting untied to an open ticket," but the source sees only sessions and ticket association. A legitimately dispatched, known session can begin unrelated or second work without creating a new session; the session-to-`in-progress`-ticket mapping still passes. The response "Known session, unassigned work" assumes knowledge the source cannot produce. The concept explicitly required an event/heartbeat contract for this moment (concept:189).

   **Resolution:** Either add a concrete work-start/heartbeat event carrying ticket and assignment identity, or limit the mechanism's claim to detecting unknown/unregistered sessions and commission semantic scope checking elsewhere.

5. **Material — The artifact-diff gate lacks a defined comparison baseline and can be satisfied while hiding silent changes.**

   **Passage:** "`mechanical git diff over spec files and Spec C anchors … at status transitions + every sweep`" (Spec B:78).

   **Evidence:** The spec does not say what tree or recorded commit is compared to what baseline, nor how an authorized raised change is associated with the resulting diff. A worker can commit before the sweep, or preserve the named headings while altering their contents, depending on the intended command. Merely mentioning anchors does not define a deterministic detection contract. Spec C's machine-readable anchors identify structures; they do not prove authorization.

   **Resolution:** Specify the immutable comparison point, files and ranges covered, how authorized changes are recorded and matched, and the exact failure result. Leave semantic interpretation to a checker, as the concept requires.

6. **Material — Several live Happier actions have neither a receipt nor an experiment despite the explicit evidence rule.**

   **Passages:** "`Per-tool live confirmation lands with E4/E2 as each mechanism first uses it`" (Spec B:25–29); dispatch/message lifecycle and "`Foreman stops the session`" (Spec B:40–61); acceptance marked complete at Spec B:178.

   **Evidence:** The concept proves only that `session_list` was exercised; the other actions were present in the catalogue (concept:364–373). E4 covers phone push and status/wait semantics. E2 covers `IS_SANDBOX` inheritance. Neither covers `session_spawn_new`, `session_message_send`, or session stopping and teardown behavior. Catalogue presence is weaker than verified operational semantics.

   **Resolution:** Add explicit experiments for each load-bearing unexercised action, or broaden an existing experiment accurately. Remove the acceptance checkmark until every runtime mechanism is covered.

7. **Material — Hermes spawning the foreman is labeled VERIFIED more strongly than the receipt supports.**

   **Passages:** "`Hermes spawns foremen targeting the host daemon (VERIFIED wiring)`" (Spec B:125); similarly Spec B:22–24.

   **Evidence:** The concept receipt establishes that Hermes is authenticated as a second machine, has the MCP entry, and successfully listed host sessions (concept:374–381). It does not report Hermes exercising `session_spawn_new` or successfully creating a foreman on the host daemon. "Wired to attempt" is verified; successful spawn-trigger operation is not.

   **Resolution:** Narrow the label to the verified authentication/visibility wiring and name an experiment for an end-to-end Hermes-triggered host spawn.

8. **Material — Spec B defines an artifact format despite assigning artifact formats exclusively to Spec C.**

   **Passage:** "`Foreman journal … append-only … as Spec C receipt-grammar lines`," followed by the claim that the harness-internal artifact is "`defined here`" (Spec B:98–101).

   **Evidence:** Spec B's declared boundary says artifact formats belong to Spec C and are "referenced by name, never restated" (Spec B:4–7). Spec C currently defines receipt lines specifically as ticket-scoped events under `## Receipts` (Spec C:63–71); it does not define the journal, journal event vocabulary, lifecycle, or authority. Calling it "internal" does not remove the boundary contradiction.

   **Resolution:** Put the journal's normative schema and lifecycle in Spec C, with Spec B referencing it, or explicitly narrow the Specs' ownership boundary and update both specifications consistently.

9. **Minor — "Fresh-spawn is always safe" exceeds the evidence and the recovery design.**

   **Passage:** "`Docs are the durable state … so fresh-spawn is always safe`" (Spec B:68–69).

   **Evidence:** Artifact authority makes fresh spawn the safer fallback, but it does not prove completeness, freshness, successful bootstrap, correct role binding, or absence of uncommitted worker state. T-004 recommends fresh spawn as fallback; it does not establish universal safety (T-004:51–56).

   **Resolution:** Narrow the claim to the intended/default recovery path and state its preconditions: authoritative artifacts current, bootstrap/brief applied, and any abandoned in-flight state deliberately discarded.

10. **Minor — "Work starting untied to an open ticket" conflicts with the specified lifecycle state.**

   **Passages:** Matrix event says "`untied to an open ticket`" (Spec B:77), while dispatch immediately changes the ticket to `in-progress` (Spec B:40–44); Spec C defines that transition as the dispatch trigger (Spec C:51–55).

   **Evidence:** Once work legitimately starts, its ticket is no longer `open`. The source correctly compares against `in-progress`, but the event wording does not. This ambiguity can produce false alarms or inconsistent implementations.

   **Resolution:** Define the event as work not tied to a valid assigned ticket whose authoritative status is `in-progress`, while separately treating unauthorized status transitions as check moment 2.

**Over-engineering:** No independent finding. The proposed repository structure is somewhat elaborate, but each listed component maps to a ticketed runtime boundary, experiment, adapter, recovery artifact, or Spec C project requirement. The more serious issue is misplaced ownership of the journal format, covered in finding 8.
