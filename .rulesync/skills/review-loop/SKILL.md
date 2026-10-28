---
name: review-loop
description: >-
  Implement, independently review, and fix a work unit through the current executor and fresh-context subagents. Use for implementation/review/fix requests or a substantial current diff; whole-spec runs finish with parallel review.
targets:
  - pi
  - claudecode
  - codexcli
  - opencode
---

# Review Loop

The current executor owns the work unit through implementation, independent review, repairs, and validation. It can be the main session or an assigned subagent; do not create another controller or writer merely to run this skill. Keep one product-code writer per worktree.

Use the requested backend and explicit provider/model/effort choices. For requested Paseo orchestration, apply [Review Loop Paseo](../review-loop-paseo/SKILL.md) once for profiles, dispatch, and notifications; it reuses this loop, not a second loop. An unavailable required role blocks the affected work; report it without silently changing providers or replacing independent review with self-review.

## Establish the work unit

Read the actual task, applicable repository rules, requirements, constraints, and relevant decisions. An explicit review-only request stays read-only; the skill name does not grant repair or delivery permission. For an existing target diff, skip implementation and start review. Reuse approved plans; resolve ordinary implementation details without re-interviewing the user. Identify the change baseline, existing edits, ownership, allowed delivery actions, and required checks. A proposed file list is navigation, not an approval gate for necessary in-scope adjacent edits.

For tasks declaring the enhanced delivery protocol, load [Delivery Protocol](../delivery-protocol/SKILL.md) and its working-set rules. Read mapped parent criteria and shared constraints, not every linked artifact. For ordinary tasks or legacy diffs, use their actual requirements and evidence; do not manufacture a spec, criterion IDs, or an evidence pack. Load references only when the next decision needs them. A `final-review` ticket goes directly to **Whole-spec acceptance**, not to ordinary implementation.

The executor may request a bounded, read-only reviewer and, when needed, a technical advisor for this work unit. Pass each only its target, requirements/constraints, accessible references, allowed actions, and return contract. Reviewers/advisors do not edit product code or delegate. Do not give them the entire orchestration manual or implementation history.

Verify that the backend can create genuinely fresh review context. A fork/resume or a new session stuffed with old chat is not independent review. If the executor cannot dispatch, an existing authorized caller may relay the request/result within the selected backend; state the limitation and keep technical handling with the executor. Otherwise report the missing capability. Do not build a new controller or bypass tool permissions.

## Implement and validate

Reconcile the work unit and constraints, then continue through implementation, in-scope fixes, and focused checks. Grounding is part of execution, not a separate approval stop for settled work. Investigate missing facts only when needed; use TDD when requested and the project's existing test seams/runners.

For cross-module changes, exercise a thin real integrated path early, including relevant failure behavior. Expand focused tests from that feedback. When a real dependency is unavailable, state what was and was not exercised; fixtures are not native E2E proof.

Identify required broad gates and one validation owner. Use the smallest relevant checks during iteration and reuse valid results across handoffs. Run required integration/full checks at the appropriate integration or delivery boundary; rerun affected checks when code, assertions, configuration, dependencies, or requirements change. Existing hooks still run. Preserve original command exits and tested state; keep raw captures in task-specific temporary storage.

Pause only affected work for a material scope/design change, uncertain side effects, missing authorization, or an unresolved decision. Do not turn a failed validation into a new recovery subsystem or other unapproved requirement.

## Review a stable work unit

When the cohesive change is review-ready, use [Parallel Review](../parallel-review/SKILL.md) with **one fresh, read-only reviewer by default**. This caller-selected count overrides that skill's standalone parallel default. Add reviewers only for requested parallel review or a material independent risk that one reviewer cannot responsibly cover.

Supply the original requirements, relevant parent criteria/constraints, correct work-unit baseline and diff, and available validation evidence. Ask for independent acceptance coverage, evidence-backed findings, and verification limits. For enhanced tickets, return local criterion judgments separately from partial parent contributions. Inspect changed/deleted tests for weakened assertions; passing tests do not alone establish acceptance.

Keep the reviewed target stable while reviewers inspect it. If it changes, disclose the new state and renew affected conclusions. Reviewers reuse valid checks and run only missing focused checks, not duplicate full suites.

## Resolve findings

The executor evaluates findings against requirements and evidence. Accept demonstrated in-scope defects; reject or defer unsupported claims, duplicates, stylistic churn, speculative hardening, and unrelated refactors with concise reasons. Do not accept every suggestion or dismiss a valid blocker to declare completion.

Repair accepted defects through the existing writer. Start with one fix pass and, when warranted, one narrow re-review using that work unit's reviewer. Mechanical low-risk fixes with adequate validation need no repeated broad review. Changed acceptance assertions, disputed findings, or material risk warrant focused re-review; test-only edits are not automatically exempt.

Continue only while there is a concrete remaining defect, a bounded repair, and a useful verification. Repeated non-progress, materially growing cost/risk, or a new decision triggers escalation, not an infinite loop. Request a read-only technical advisor for a specific design/diagnostic question with evidence and alternatives. Advisors do not authorize scope changes or operational actions. Ask the user through the current task's communication path when a human decision or permission is needed.

A failed agent turn is not itself a failed review, and a successful turn is not acceptance. Retain usable findings, inspect coverage, and supplement only important missing evidence. Missing required review/checks stay unverified; do not silently call them clean.

## Complete and return

Complete only when the work unit's acceptance criteria and required checks pass, accepted blockers are resolved, and the delivered state matches the evidence. Persist a concise record in the existing ticket/evidence location; direct tasks can use their normal report. Record accepted/rejected findings and any remaining limits without duplicating full transcripts.

Perform already-authorized documentation, commits, and other delivery actions together after verification; preserve unrelated edits and required hooks. This does not grant push/deployment permission. Operational runs may continue through successful authorized stages without per-command approval, but stop on unexpected failure or uncertain effects rather than blindly replaying operations.

Return the work unit, `complete / blocked / partial`, evidence location and tested state, delivery status, and any blocker/next action. `review-ready` is an internal checkpoint, not a completed issue report. Another caller needs this receipt, not the full implementation/review history. Completion of a local slice does not establish whole-spec acceptance.

## Whole-spec acceptance

For an authorized batch, respect dependencies and ownership; do not extend a single-ticket request to the remaining backlog. After delivery prerequisites complete, verify the integrated feature against every current parent requirement and shared constraint, using the feature baseline rather than the last ticket's base. Enhanced tasks use [Final Acceptance Review](../delivery-protocol/references/final-acceptance-review.md); legacy work records equivalent evidence with its existing parent.

Reuse valid deterministic checks and complete missing required checks. At this final stage, **automatically invoke parallel-review by default**, normally with two fresh reviewers and additional views only as justified by risk or explicit user choices. Ensure whole-spec coverage and cross-ticket integration are examined; specialist views complement, not replace, acceptance judgments. Include an architecture view when useful instead of adding a mandatory Architecture Review after the parallel group.

Synthesize the evidence, route accepted repairs to the authorized writer, and renew only affected validation/review. Parallel findings alone do not prove all criteria pass. Keep final acceptance open while required claims remain partial or unknown. Explicit user overrides apply; unavailable review capability must be reported, not disguised as completion.

This automatic final-review policy belongs to this workflow. Manual ticket execution does not invoke it implicitly: the user chooses whether to request parallel review, while the same whole-spec completion conditions still apply.
