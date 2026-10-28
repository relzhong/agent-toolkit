---
name: review-loop-paseo
description: >-
  Run review-loop through Paseo with a thin Lead dispatching issues and Implementation owning each issue's review/fix cycle. Use when Paseo orchestration is requested.
targets:
  - pi
  - claudecode
  - codexcli
  - opencode
---

# Review Loop Paseo

Apply [Review Loop](../review-loop/SKILL.md) once for implementation, findings, validation, and acceptance. This adapter adds Paseo roles and transport, not another review loop. Load the installed `paseo` skill for actual tool contracts. Load delivery-protocol only when the task uses that contract, as described by the shared loop.

## Roles and setup

- **Lead:** dispatches ready issues, tracks dependency/ownership state, handles escalations, checks completion receipts, and organizes whole-spec acceptance. It does not approve every command, re-review every diff, or relay routine review/fix phases.
- **Implementation:** owns the assigned issue's shared loop, including self-tests, reviewer calls, finding disposition, repairs, and authorized delivery. It may dispatch read-only reviewers/advisors for this issue, not unrelated work or recursive agent trees.
- **Basic Review:** fresh-context, read-only issue review and warranted narrow re-review.
- **Architecture Review:** read-only advice for a specific difficult technical question, or a justified architecture view in final parallel review; not a mandatory approval stage.

Read saved profile notes and materialize the profiles actually needed through the Paseo skill. Preserve explicit provider/model/mode/effort/features. Carry applicable role constraints into the task prompt; if legacy workflow notes forbid delegation or route every repair through the Lead, explicitly reconcile them with this user-selected issue-owned workflow. Do not forward contradictory instructions or edit saved profiles implicitly; tool permissions and explicit user restrictions still apply. The role names above are defaults, not bundled machine configuration. An unused optional profile is not a prerequisite. A required selected profile that cannot start blocks that workstream; report the exact limitation without substituting another provider, self-review, or an unrelated specialist.

Check whether the assigned Implementation can use Paseo tools to create fresh reviewer sessions and receive their results. Profiles or skill text do not grant missing tool permissions. If only the existing Lead can dispatch, explicitly use it as a transport relay while Implementation retains technical handling. If neither route can provide the requested review, report the capability blocker. Do not change backend silently or implement a scheduler as a workaround.

Once per run, identify the selected daemon and relevant tool contracts. Recheck the same host after reconnect/upgrade; client or relay versions do not establish daemon behavior. Do not alter saved profiles or upgrade the daemon as an implicit setup step.

## Dispatch a complete issue

The Lead sends a ready issue to Implementation with:

- Its path, dependency state, worktree/workspace, issue baseline and existing-edit boundary. Keep the feature baseline separately for final acceptance; include relevant untracked changes when establishing the starting state.
- Approved requirements/constraints and selected decision/evidence pointers, accessible in that checkout. Do not copy the full previous conversation or every profile's notes.
- Permission to execute the whole issue loop, including bounded read-only reviewer/advisor delegation; validation/evidence destination, delivery authorization, and escalation conditions.

Use one active assignment per worker, with a stable ID such as `03/implement/1`. Map it to the agent and evidence location in the existing task record, not a parallel bookkeeping tree. Implementation gives its own review/advice requests scoped IDs and handles their replies. Reviewers receive only their read-only target, relevant constraints, evidence, and return contract; they do not need the entire Lead workflow.

For multi-ticket implementation, keep the existing default of one commit per accepted issue, with hooks enabled; honor an explicit no-commit instruction. Implementation performs authorized commits after its issue loop passes, without another Lead release. Push/deployment still needs authorization. With no commit, identify a stable patch/tested state instead.

Default to a new Implementation session for each issue, retaining the writer for its repairs. Reuse for tightly related work only with a stated context benefit; normal compaction inside useful ongoing work is allowed. Use fresh review sessions for unrelated issues, not resumed histories. Keep one writer per worktree; when parallel implementation is authorized, preserve the existing maximum of two independent writers (or the user's lower limit), separate worktrees, and no likely overlap in interfaces or coupled behavior.

## Results and notifications

The agent dispatching a direct child owns its transport bookkeeping: Lead for Implementation, Implementation for reviewer/advisor. Set `notifyOnFinish: true` and use asynchronous follow-ups. If relaying for a tool-limited executor, the Lead forwards the scoped result, not a second technical judgment.

Check the dispatch acknowledgment before waiting. On ambiguous acceptance or timeout, reconcile the assignment once against current status/latest activity: await it if running, consume it if completed, retry only when confirmed not started and the cause is resolved. Unknown state is a reported blocker, not permission to duplicate a writer.

After successful dispatch, do other authorized work. With none available, give a concise pending-state handoff and end the turn so the completion/permission/error notification can resume it. This is waiting, not completion. Do not poll repeatedly, keep the model awake with sleeps, or add duplicate heartbeat notifications.

Match each incoming result to its agent/assignment and current target state. Preserve usable findings even if transport later errors; review actual evidence and fill only missing coverage. Permission pauses and ended turns are not acceptance. Ignore already-handled/superseded events; inspect genuinely new evidence without redoing completed actions.

After restart or connection loss, reconcile saved ownership with agent state, latest result, Git state and validation once. Resume only unfinished work. Never resend implementation merely to restore a missing notification; preserve the writer until its state is known. Report unsupported automatic resubscription/continuation instead of pretending recovery is guaranteed.

## Delivery and escalation

Implementation maintains the issue's existing evidence record and returns a compact receipt: assignment/issue, `complete / blocked / partial`, evidence path and tested state, review/fix outcome, commit or patch boundary, and any blocker. Keep detailed findings and logs out of the Lead's chat; raw captures belong in task-specific temporary storage. `review-ready` is internal to Implementation, not its final return to the Lead.

The Lead checks required completion evidence and authorized delivery actions, then releases dependents. Missing evidence needs supplementation; a known defect cannot be waived. It does not repeat all technical judgments or read every worker transcript. Keep independent authorized work moving when another issue is blocked.

Implementation consults Architecture Review only with a concrete question, evidence, and alternatives. Technical advice returns to Implementation. Scope, requirement, permission, or unresolved material-risk decisions go to the Lead for a concise user question; advice cannot replace user authorization. Ordinary repairs and successful authorized operational stages continue without repeated releases. Unexpected effects or uncertain replay stop affected work.

At the whole-spec boundary, the Lead organizes the shared loop's **automatic final parallel review**, after required deterministic checks. Select existing profiles appropriate to the assigned views; do not always append a separate Architecture Review. Findings return to authorized writers for bounded repair and affected revalidation. Record whole-spec acceptance separately from agent completion or specialist opinions.

Keep the Lead's current-state index limited to dependencies, owners, results, decisions needing action, and evidence pointers. At a safe batch boundary, use supported controller handoff if renewal is needed; never restart active workers just to refresh context. Report milestones and real blockers, not every internal handoff. Claim savings only when observed; this adapter does not promise automatic cross-session scheduling or lower cost.
