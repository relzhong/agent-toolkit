---
name: parallel-review
description: >-
  Review a diff, plan, or implementation with fresh-context reviewers and evidence-based synthesis. Use for parallel/adversarial review or as the reviewer-selection method inside review-loop.
targets:
  - pi
  - claudecode
  - codexcli
  - opencode
---

# Parallel Review

Provide independent inspection and a synthesized report, not another implementation loop. The caller may be the main session or a delegated executor; it need not be a top-level controller. Reviewers remain read-only and do not delegate.

## Scope and coverage

Read the requested target and applicable instructions before dispatch. Identify the requirements, stable revision/diff boundary, available evidence, and independent failure domains. A plan or document review uses its own source and correctness criteria; do not require a code diff or executable tests where none apply.

Use the caller's explicit count and angles. For standalone parallel review, default to **two** reviewers; add a third for a materially distinct risk. A workflow reusing this method may select **one** reviewer for an ordinary issue. Honor user-specified counts; do not manufacture generic specialties merely to reach a default number.

Assign scopes before dispatch, without asking the user to manage reviewer selection. For code/issue review, ensure requirements, correctness, and regression coverage have an owner; additional views focus on actual independent risks. Useful domains include security/privacy, persistence/concurrency, contracts/integration, test adequacy, UX/accessibility, and documentation fidelity. Simplicity findings must identify a concrete cost or defect, not generic preferences. A single reviewer covers the whole required scope rather than only one narrow specialty.

For whole-spec review, map all current acceptance requirements and cross-ticket behavior to the review scopes. Avoid both duplicated full reviews and gaps caused by everyone inspecting only a specialty.

## Dispatch

Use fresh context unless the user explicitly requests otherwise; disclose inherited context rather than presenting it as independent. Supply each reviewer only:

- The target, stable base/revision, assigned coverage, and original requirements/constraints.
- Accessible source and validation-evidence pointers; not the author's implementation chat or self-assessment as a substitute for inspection.
- Read-only/no-delegation scope and the result contract below.

Ask for actionable findings with severity, file/section evidence, the violated requirement or plausible failure path, impact, and smallest safe repair. Distinguish blockers, fixes worth doing now, optional observations, and checks that remain unverified. No findings is a valid result; do not demand a quota or broad best-practice essays. Where the caller requests criterion judgments, include their evidence and limits.

Run independent views concurrently when supported. Reviewers inspect actual sources, reuse valid evidence, and run only missing focused checks. Assign broad/expensive validation once to an authorized owner. Keep the target stable during review; relevant changes invalidate affected conclusions.

## Synthesize

The caller checks findings against evidence and scope, merges duplicates, and returns accepted defects, decisions requiring the user, optional improvements, and rejected/deferred feedback with concise reasons. Resolve conflicting claims by inspection or a focused check, not majority vote. This is targeted synthesis, not another full review of everything.

Agent completion/error status is not a correctness verdict. Preserve usable results even after a transport failure; retry only missing material coverage, not the entire group. Report unavailable checks and truncated/missing evidence honestly. Do not label an incompletely reviewed target clean.

## Authorized fixes

Standalone review is read-only unless the user explicitly requests `autofix` or otherwise authorizes fixes. Quoting or discussing the word is not authorization. A caller already executing an authorized implementation/fix loop needs no repeat approval for routine in-scope repairs.

Return findings to that existing writer. If standalone autofix has no writer, use one authorized writer for accepted fixes only; reviewers never switch into writers. Run affected validation and report the resulting state. Optional improvements require separate authorization.

Do not start another review-loop from this skill or automatically repeat the parallel group. The calling execution workflow owns any warranted targeted re-review and final acceptance. Without repair authorization, report the findings and request permission only if the user wants them applied.
