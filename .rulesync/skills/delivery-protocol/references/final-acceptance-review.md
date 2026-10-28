# Final Acceptance Review

Read with [Delivery Context](delivery-context.md) when creating or executing the final ticket. Whole-spec acceptance is required for the enhanced workflow. This protocol defines coverage, evidence, and closure, not reviewer count, agent roles, or automatic invocation. The selected execution workflow determines the review method; manual execution leaves the choice of parallel review to the user.

## Ticket contract

Create one final issue with `Type: final-review`, `Status: ready-for-agent`, parent/protocol links, and explicit dependencies on **all** delivery tickets. Reuse it when the issue set changes. Include:

- Parent delivery index, all AC IDs/revisions and shared constraints, relevant confirmed decisions, and links to delivering tickets for every AC.
- The review base identifying the start of the feature's changes and the final integrated state to inspect. Record a known commit/base before implementation when possible; for pre-existing work, recover the real base from its history. Do not use the latest ticket's base as the whole-feature baseline. Mark an unknown base for resolution before claiming diff-wide review.
- Required deterministic/integration/manual checks and their existing evidence locations; inspect the repository/task requirements rather than inventing a blanket full-suite requirement.
- A results table: `parent AC + revision | PASS / FAIL / PARTIAL / UNKNOWN | independently checked evidence | validated state`.
- Completion conditions: all current parent ACs pass, shared constraints hold, required integration checks pass, accepted blockers are resolved, and the reviewed state is the state being delivered.

Store the baseline, commands, results, findings, fixes, outstanding checks, and final state in the ticket. Spec closure and any implemented-planning note must link this final evidence; complete delivery tickets alone are insufficient. Confirmed requirement changes follow criterion revision rules, not an implicit waiver by the reviewer.

## Execute

1. Verify dependencies and artifact accessibility. Read the parent index and shared constraints, then inspect **every current AC's original text**, in batches if useful. Look for requirements lost in decomposition, missing mappings, and stale revisions. Final review owns finding uncovered requirements even if no child ticket mentions them.
2. Independently assess evidence against requirements and inspect the actual integrated implementation/diff. Previous `PASS` reports are leads to evidence, not proof by themselves. Follow relevant decisions and drill into reference code only when needed. Check cross-ticket behavior and tests for weakened assertions or mismatched expectations.
3. Reuse valid checks and identify missing or invalidated ones. Run or request the missing checks within the current authorization and execution workflow, avoiding duplicate broad validation. Report unavailable required checks as `UNKNOWN`; neither specialist opinions nor parallel findings substitute for requirement evidence.
4. Record per-AC judgments and additional evidence-backed correctness findings separately. Judge shared constraints even when not given separate AC IDs. Known environmental unavailability is not a new bug, but it does not make an unverified requirement pass.
5. Mark final review complete only when its completion conditions hold. Report residual non-blocking risks separately. If integrated changes occur after review or validation, revalidate the affected claims before closure.

## Findings and fixes

**Authorized automatic repair:** use the selected execution workflow to resolve accepted in-scope defects, renew affected checks, and obtain any warranted re-review. Preserve single-writer ownership and authorization boundaries. Unresolved blockers keep final review open; this protocol does not dispatch agents or start a repair loop.

**Manual ticket execution:** the review pass reports findings and leaves final review open; it does not start an automatic fix loop. Record actionable, in-scope defects as linked repair tickets, reusing existing ones where appropriate. Repairs depend on the relevant delivery work, not on completion of the final review; the final ticket then depends on those repairs, avoiding a dependency cycle. Product/scope/architecture questions go to the user before being specified as approved repairs.

When the user explicitly requests a fix, perform the authorized fix and focused validation, update its evidence, then remind the user that final review must be run again. Leave that review decision to the user unless they explicitly requested automatic review/fix orchestration. Persist the review result in the existing artifact. A read-only review assignment does not authorize product edits; fixes require an authorized writer under the selected workflow.
