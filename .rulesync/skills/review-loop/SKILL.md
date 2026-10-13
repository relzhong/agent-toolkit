---
name: review-loop
description: >-
  Run a Lead-controlled implementation/review/fix loop with grounded inputs, acceptance evidence, one writer and one reviewer by default, and focused validation. Use when asked to implement then review, review until clean, run a review/fix loop, or validate a substantial current diff.

targets:
  - pi
  - claudecode
  - codexcli
  - opencode
---

# Review Loop

Load the installed `delivery-protocol` skill once before using the shared references below. Resolve project paths from the target project root, not this skill directory. If a sibling skill link is unavailable, locate that installed skill by name; do not assume an agent-specific installation path.

## Purpose

Run a cost-aware workflow controlled by the parent AI agent (the Lead):

```text
optional investigation → grounding → implementation → acceptance/risk review
→ Lead synthesis → accepted fixes → affected validation/re-review → delivery
```

Keep one writer per active worktree. Children must not manage the loop or delegate unless explicitly assigned a bounded fanout role. Ask the user about product/scope/architecture decisions, uncertain continued repair, required manual actions, or blocked execution routing; routine implementation, checks, and well-understood in-scope fixes proceed within existing authorization.

## 1. Establish Inputs and Execution Roles

Read [delivery-context](../delivery-protocol/references/delivery-context.md). Identify the ticket, parent spec, assigned local criteria and parent AC revisions, mandatory constraints, and selected context anchors. Load the mandatory working set; drill into reference code only when needed. Preserve confirmed requirements if a ticket compressed them incorrectly. Repository instructions and confirmed design decisions remain applicable; evidence labels do not create new requirements.

For legacy tickets/direct diffs without a parent, use the actual approved user requirements and available acceptance criteria, state what is missing, and avoid claiming whole-spec closure. Ordinary clear tasks need no invented spec, AC migration, or evidence pack. Missing information that prevents correctness judgment needs clarification. A selected `Type: final-review` ticket goes directly to the final acceptance procedure in step 7, not to an implementation worker.

Honor the user's selected backend and named profiles. Availability alone does not select Paseo. With requested Paseo orchestration, apply [Review Loop Paseo](../review-loop-paseo/SKILL.md) for dispatch, worker lifetime, notifications, and ticket delivery. Read each skill once; when entered through that wrapper, continue this shared procedure without routing back. Otherwise use available native fresh-context agents for the same roles, unless the user selected another execution arrangement.

An explicitly selected profile/provider/model is binding. If it cannot start or is quota-, rate-, authentication-, or availability-blocked, preserve state, stop that workstream, report the exact role and reason, and ask how to proceed. Other independent work may continue only if it does not require that role. Do not silently change models/thinking levels, substitute native agents, use Architecture Review as a fallback, or let the Lead become the unavailable worker without explicit authorization.

Profile notes guide role selection; they are not automatically the worker's instructions. The Lead must put the applicable role constraints into the actual task prompt (`initialPrompt` on Paseo `create_agent`, or the equivalent native delegation prompt), together with:

- Role, current phase, and allowed actions: read-only investigation/review/grounding, implementation, accepted fixes, or validation only.
- Work unit, parent spec when present, assigned criteria/AC revisions, and durable paths to applicable repository instructions, mandatory decisions, and on-demand references. Verify those paths are accessible in the worker's checkout.
- Approved scope, invariants, and explicit exclusions; include only the selected role's relevant constraints rather than every profile's notes.
- The phase-specific return contract from the steps below: grounding summary, investigation evidence, criterion judgments/findings, or implementation/validation results.
- The stopping condition and whether to wait for Lead authorization. State that the child must not delegate or manage the loop unless this assignment explicitly permits a bounded fanout.

On follow-up, explicitly identify any phase transition and newly allowed actions. In particular, an Implementation profile assigned read-only grounding must wait for the Lead's implementation instruction before editing. Conversation inheritance and selecting a profile do not replace this task contract.

## 2. Investigate Only When Needed

Skip investigation when requirements, ownership, files, and direction are clear. Otherwise use one read-only scout/Investigation worker to inspect the missing facts, test competing hypotheses when diagnosing, rule out causes, and return only relevant files/symbols, evidence, uncertainty, focused commands, and the best next step. Investigation does not make speculative production fixes.

Before escalating an unresolved high-impact problem to Architecture Review, gather a concise evidence package through the appropriate cheaper role. For queued dependency-safe work, read-only investigation may run alongside the active writer; avoid scouting assumptions still being changed by prerequisites.

## 3. Ground, Then Implement

The Lead selects the grounding mode in the dispatch. Use **read-only grounding** when requirements conflict, ownership/trust boundaries are unresolved, or implementation direction needs a decision. The worker returns the work unit, criteria/revisions, anchors read, invariants, edit surface, exclusions, and uncertainties, then waits for explicit Lead authorization.

For settled work, the Lead may authorize **ground then implement** in the initial assignment. Before editing, the worker reconciles that same working set and records a concise grounding result in its evidence. It continues only while the supplied scope and decisions hold; a mismatch or material uncertainty returns the affected work to the Lead. Include any documented rejected approach or critical counterexample relevant to the ticket. The Lead checks grounding with implementation evidence before accepting the ticket. This removes a routine message round trip, not the grounding or acceptance obligation. Escalate genuine new decisions to the user; continue unaffected work.

The authorized writer implements only approved scope, uses TDD when requested, and runs focused validation. If direction becomes materially unclear, return to investigation rather than making broad speculative changes. If the existing diff is already the target, skip writing and begin review with the working set; do not retroactively treat the diff as proof of the intended requirements.

The writer reports changed files, commands/exit codes, criterion evidence tied to the tested state, remaining risks, and anything undone. Treat the report as review input, not automatic completion.

## 4. Review Acceptance and Risk

Use one fresh-context, read-only reviewer by default. It independently checks **all local criteria**, their parent requirements, applicable shared constraints, actual diff, and supplied evidence. Select an additional risk focus using [Parallel Review](../parallel-review/SKILL.md); that specialty does not limit acceptance coverage. A parent constraint lost from the ticket is still in scope when applicable to this change.

For each local criterion return `PASS`, `FAIL`, `PARTIAL`, or `UNKNOWN` with evidence and its limits. Record parent contributions separately: a complete local slice may leave a parent AC `PARTIAL` until integration. Then report additional actionable findings with severity, file/line evidence, impact, and the smallest safe fix. Inspect changed/deleted tests for weakened behavior assertions; a passing edited test is not automatically valid acceptance evidence.

Add another reviewer only for explicitly requested parallel/adversarial review or an independent material risk the assigned reviewer cannot responsibly cover. Reviewers inspect evidence and may run missing focused checks; the Lead coordinates expensive/full checks through the appropriate authorized execution role. Reviewers do not edit implementation or apply their own fixes.

## 5. Synthesize and Manage Repairs

The Lead accepts only demonstrated, in-scope correctness/requirement findings, including relevant persistence, authorization, concurrency, retry/dispatch, data-loss, privacy, public-contract, or financial-integrity defects. Reject speculative hardening, unrelated refactors, stylistic cleanup, duplicates, and future-proofing. A known unavailable check is not itself a code defect, but a required unverified criterion remains `UNKNOWN` and cannot be called complete.

Route accepted fixes to one writer, preferably the original Implementation worker. Preserve scope and update affected evidence. Use narrow re-review for material risk or a disputed/changed acceptance assertion. Re-review only the findings, fixes, and affected criteria. Mechanical low-risk fixes with sufficient validation need no repeated broad review; test-only changes are not categorically exempt.

Start with one fix pass and at most one targeted re-review of that pass. The Lead may authorize a further pass when evidence identifies a concrete remaining defect, a bounded repair, and a specific verification likely to resolve it; briefly state that expectation. If the next repair is uncertain, previous passes repeat without progress, cost/risk materially grows, or a new decision is needed, preserve the state and ask the user whether/how to continue. A round limit or optimism never turns unresolved acceptance into success.

## 6. Validate and Complete the Work Unit

Identify required checks from the task, repository scripts, and hooks at setup, including the intended delivery action. Name one full-validation owner; other workers run only assigned focused checks. During TDD use the smallest check that proves the current behavior through the repository's standard runner and admission. Preserve required real database and integration evidence.

Coordinate required expensive/shared gates once at the appropriate final integration or pre-commit point while their evidence remains valid. For each result record command/exit, exercised behavior and tested state in the existing evidence record. After relevant code, assertions, configuration, dependency, or criterion changes, rerun affected checks, including the full gate when necessary. Required hooks still execute at commit/push; an earlier check does not waive them. The Lead assigns execution to the authorized validation/implementation role; Architecture Review consumes those results rather than running the gate itself.

Before completing a delivery ticket, confirm every local criterion and required check passes, accepted blockers are fixed, and the final diff remains in scope. Persist the execution/evidence record required by Delivery Context, applying the artifact lifetime and commit boundary in [delivery-artifacts](../delivery-protocol/references/delivery-artifacts.md). Partial parent ACs remain visible. Commit after each completed ticket when requested; validate the state actually being committed/delivered.

## 7. Multiple Tickets and Final Acceptance

The Lead controls the dependency graph. Run dependent tickets sequentially and only after prerequisites complete. Independent writers may run concurrently only when explicitly authorized, with separate worktrees and no likely overlap in files, interfaces, migrations, shared contracts, or coupled behavior. Keep at most two implementation writers active, or the user's lower limit. Integrate their results before validating the combined state.

For a whole-spec assignment, execute the final review ticket using [final-acceptance-review](../delivery-protocol/references/final-acceptance-review.md) after all delivery prerequisites complete. If working from a legacy issue set, record the equivalent final acceptance evidence with the parent instead of manufacturing a new workflow tree. A request for one delivery ticket does not authorize implementing the rest; report remaining final acceptance work.

The Lead owns whole-spec acceptance, checks every current parent AC and shared constraint, and persists the result. After deterministic integration checks pass, use a fresh Architecture Review only for substantial integrated changes or material design/public-contract/persistence/concurrency/security/cross-module risk. Supply the integrated diff and grounded evidence. Small local changes still receive final requirement verification without a mandatory architecture specialist. Any resulting fixes return through step 5 and invalidate affected validation/review claims.

## Finish

Summarize work units and order/parallelism, backend/profiles used, investigation and grounding, local/parent acceptance status, accepted/rejected findings, repairs and continuation decisions, validation/state, commits when requested, whether Architecture Review was used and why, residual risks, and any unrun checks or stop reason. Link the durable ticket/final acceptance record so a manual session can resume.
