---
name: review-loop-paseo
description: >-
  Run review-loop through saved Paseo profiles with ticket-scoped workers, concise handoffs, completion notifications, and per-ticket commits. Use when review-loop is requested with Paseo delegation.
targets:
  - pi
  - claudecode
  - codexcli
  - opencode
---

# Review Loop Paseo

Load the installed `delivery-protocol` skill once before using the shared references below. Resolve project paths from the target project root, not this skill directory. If a sibling skill link is unavailable, locate that installed skill by name; do not assume an agent-specific installation path.

Apply [Review Loop](../review-loop/SKILL.md) for grounding, acceptance, findings, repairs, dependency safety, and final review. Read it once; if reached from that skill, continue the same workflow. This wrapper selects Paseo operations and delivery defaults, not a second review loop. Explicit user choices override these defaults. Load the installed `paseo` skill for current tool semantics; children receive bounded assignments and must not delegate or manage the loop.

## Set up the run

Read saved profiles and their notes before dispatch. Materialize the selected profile through the installed Paseo instructions; retain its provider/model, mode, thinking option, and features. These profile names are workflow defaults, not bundled machine configuration. Use these named roles unless the user explicitly selects others:

| Profile | Assignment |
| --- | --- |
| Implementation | Clear implementation, accepted fixes, and coordinated validation |
| Investigation | Read-only evidence gathering when root cause, ownership, runtime behavior, or direction is materially unclear |
| Basic Review | Fresh-context, read-only ticket review and warranted narrow re-review |
| Architecture Review | Material design escalation, unresolved high-impact diagnosis after cheaper investigation, or substantial final integrated review after deterministic checks |

The shared loop's binding-profile and blocked-routing policy applies. An unavailable named role is a blocked workstream, not permission to choose the closest profile. Preserve state and report the exact role/failure to the user; independent work may continue only within that policy. The Lead remains controller and acceptance owner, not a substitute implementation/review worker. Routine checks belong to Implementation, not Architecture Review.

Record the dependency graph, feature baseline, delivery intent, active assignments and full-validation owner in the existing ticket/index; avoid a parallel bookkeeping tree. For a multi-ticket implementation, default to committing each accepted ticket with hooks enabled. Push/deployment still requires authorization. If the user requested no commits, retain a stable ticket patch/state boundary instead.

Once per run, identify the selected daemon and current tool contracts. After an upgrade/reconnection, verify that same host before resuming; CLI, client, or relay versions alone do not establish the agent daemon's version or notification behavior. Use supported tools without assuming an upgrade adds reliable replay or deduplication.

## Dispatch and worker lifetime

For each ticket, pass the shared dispatch contract plus:

- Workspace, ticket base, current diff boundary, and predecessor interfaces/evidence. Keep the feature baseline separately for final review. With pre-existing changes, capture the starting patch including relevant untracked files; do not attribute the entire cumulative feature diff to this ticket.
- Exact ticket/LC revisions, parent shared constraints and mapped AC text locations, required decision anchors, approved exclusions and critical counterexamples. Verify paths in that checkout. References stay on demand; short prompts do not replace mandatory requirements.
- Grounding mode and edit permission, expected validation, evidence destination, concise return format, and stopping condition. Pass the requested TDD skill and relevant test guidance to the writer.
- For test, build, and hook commands, tell workers to use 20–30 second yield windows while waiting; report completion as soon as it occurs. Use shorter windows when the work is genuinely interactive or time-sensitive.

Give each assignment a stable ID such as `03/implement/1`, mapped to its agent, phase, and evidence location in the existing record. Have the worker echo it in results. Keep one active assignment per worker; send the next phase after consuming the prior result. Necessary corrections refer to the active ID and superseded instructions, rather than dispatching a second task.

Check the dispatch result before waiting. A successful acknowledgment needs no extra status poll. On timeout/error or ambiguous acceptance, reconcile that assignment once against current status and latest activity: if it started, await its result; if completed, consume it; if confirmed not started, retry only when the failure is resolved and routing permits. If still unknown or blocked, report that now rather than claiming work is running or waiting for a completion notification that may never arrive. Permission pauses remain pending permission, not successful execution.

Default to a new Implementation session for each ticket; reuse its writer for accepted fixes. Reuse for a tightly related next ticket only with a stated context benefit. At the next completed-ticket boundary, renew sessions dominated by completed history, repeated compaction or a domain change. Apply the shared fresh-reviewer rule to Basic and Architecture Review; a new design question outside the prior review scope gets a new session. Pass current decisions and evidence links, not the whole conversation. Preserve the requested profile and active repair ownership; session renewal is not model fallback.

Advance dependent tickets only after prerequisite acceptance, required checks, and the requested commit complete. Apply the shared concurrency rules: at most two writers, separate worktrees for concurrent writers, and no likely overlap in files, contracts, migrations or coupled behavior. Parallel read-only preparation may not assume unfinished prerequisite behavior is settled.

## Handoffs and notifications

The writer maintains one authoritative evidence record with grounding, LC judgments, changed files, exact checks/exits/state, repairs and remaining gaps. Its completion message gives the assignment ID, result status (`review-ready`, `blocked`, or `partial`), evidence path/section and tested state, meaningful changes, blockers and next action. Aim for at most 2,000 characters; keep blocker details explicit rather than silently truncating them. Read the referenced evidence directly instead of fetching activity merely to repeat the summary.

Reviewers remain read-only, including evidence files. Request one report covering all assigned criteria, with compact judgments, source/test references, actionable findings and limits. A long report can have sections without separate dispatches or approval stops. The Lead persists judgments and synthesis in the existing record. Fetch a truncated result once and read only missing sections thereafter.

Set `notifyOnFinish: true` on creation and asynchronous follow-ups; use `background: true` for follow-ups. After dispatch, do independent authorized work. When none remains, give a concise pending-state handoff and end the current turn so the completion/permission/error notification can resume control. This is a waiting checkpoint, not task completion. Do not keep the model active with repeated sleep, status polls, or unchanged progress messages.

On notification, match agent, assignment/phase and result state before advancing. Track permission requests separately by request ID; permission pauses and an ended agent turn are not ticket acceptance. Record the handled result and next action in the existing record. Ignore already-handled or superseded results; a genuinely new result for the same assignment still needs evaluation. Resolve missing identity with a targeted evidence/activity read, not a guess. This prevents duplicate actions, not duplicate tokens already delivered to the model. Use only the built-in completion channel; do not add a heartbeat or plugin that also wakes the Lead for the same event.

After a daemon restart, connection loss or controller-session recovery, reconcile the saved assignment with current agent status, its latest result, Git state and validation evidence once. Resume only the unfinished phase; do not recreate workers or repeat commits/gates just because a notification is missing. Keep the original writer's ownership until its state is established. If it is still running and a supported completion subscription cannot be restored, preserve the checkpoint and report the automatic-continuation limitation. Never resend implementation solely to rearm a notification or replace recovery with timer polling. Missing evidence remains unverified.

## Keep implementation feedback focused

Use the shared validation plan and repository admission. Run the target project's canonical validation commands through its existing admission and output tools when present. For verbose commands, keep the complete permitted output in a task-specific temporary log and return a bounded summary with the original exit status. Do not assume a particular package manager, script path, or validation wrapper exists. Return that result and the full log path in durable ticket evidence. Reviewers reuse valid results and request missing focused checks; the single full-validation owner coordinates broad gates, including across worktrees.

Before rerunning checks after a repair, identify which behavior and evidence the change invalidates. Preserve behavioral red→green; passing unchanged broad checks need not repeat at every handoff. Diagnose validation-tool failures in the same assignment, retaining the actual failed value and expected contract before retrying. Environment/runner optimizations requiring new configuration or test architecture remain separate scope decisions.

For storage-heavy validation, check capacity before building. Reuse valid artifacts; under existing cleanup authority remove only task-owned, reproducible intermediates no longer needed by active work or review. Preserve source, patches and required evidence. Keep captures outside the repository; avoid a new resource-management subsystem.

If implementation direction becomes unclear or repairs repeat without progress, return a minimal reproduction, tested hypotheses, observed failures, and the next useful diagnostic step to the Lead. Route genuine diagnosis to Investigation under the shared continuation policy; clear in-scope fixes continue through the original writer.

## Complete delivery

For each ticket implement and validate, then obtain Basic Review and Lead acceptance before committing or releasing dependents. Fixes, affected validation and narrow re-review are conditional on actual findings, not mandatory extra phases. The Lead may perform the authorized Git operation after writer handoff; reviewers never mutate files. Preserve unrelated changes.

After integration, execute the required final deterministic gates before a warranted Architecture Review. Apply resulting repairs and renew affected evidence. Record final whole-spec acceptance separately from specialist review. Finish with the shared summary plus profile/session mapping, ticket bases/commits, worker reuse decisions, and any pending notification/routing/manual blocker. Report observed usage when available; do not infer monetary savings from cached input or claim an unmeasured efficiency gain.
