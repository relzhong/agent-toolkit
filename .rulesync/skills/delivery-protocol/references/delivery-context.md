# Delivery Context

Use this protocol when producing or executing tickets from the installed `/to-prd` and `/to-issues`, or when `/review-loop` has a parent spec. Small direct tasks can use their explicit user requirements without manufacturing a spec or ticket tree. The user chooses which workflow to invoke.

## Responsibilities

- **Spec** owns current confirmed requirements and parent acceptance criteria (ACs).
- **Confirmed decisions and applicable ADRs** own design constraints. Repository instructions remain applicable throughout; they are not subordinate to reference material.
- **Ticket** owns the current work unit, local acceptance criteria, dependencies, and links to parent ACs.
- **Planning** retains design reasoning, rejected alternatives, and open questions. Mark superseded decisions explicitly.
- **Evidence** retains sources, observations, excerpts, and their trusted scope. A label such as `Canonical` or `MUST FOLLOW` cannot promote an agent's inference into an approved requirement.

Follow explicit supersession records. A ticket may narrow the work unit but cannot weaken its parent's requirements. Report a ticket/spec mismatch and preserve the confirmed requirement; if correcting it requires a product, scope, or architecture decision, ask the user and pause only affected work. Lead approval of grounding does not authorize new constraints or weaker acceptance.

## Load a working set

Read in this order, expanding only when the next decision needs it:

1. The current ticket and applicable repository instructions.
2. Its parent's short entry/index, **all shared constraints**, and the original text of the mapped ACs. A parent link is mandatory for enhanced tickets; reading every unrelated AC is not.
3. The ticket's required decision/trap anchors. Each pointer must say why and when to read its target.
4. Reference code, excerpts, detailed investigations, and raw logs when their read condition applies. A reference is not automatically a copy target.

Use headings, symbols, and targeted file reads rather than loading every linked file. An approved invariant discovered in a reference must also be reachable in the mandatory constraint/decision layer; optional reference reads must never hide an obligation. Missing optional references can be reported without blocking unrelated work. Missing required requirements or decisions block the affected part until recovered or clarified.

For fresh sessions and delegated workers, pass these durable paths and IDs explicitly. Verify they are readable in the worker's checkout before work starts; uncommitted `.scratch` artifacts do not automatically appear in another worktree. Transfer only the needed artifacts within the authorized workflow or use a checkout containing them, preserving their identity and links.

## Stable criteria and revisions

Parent IDs (`AC-01`, ...) are stable within a spec. Local IDs (`LC-01`, ...) are stable within a ticket; cite them with the ticket path/ID. Do not renumber or reuse removed IDs.

Give each criterion a revision, starting at `r1`. A user-approved change in observable meaning increments its revision; a replacement can instead receive a new ID with an explicit supersedes link. Record the decision and identify affected tickets/evidence. Mark affected old results as needing revalidation. Editorial clarification that preserves meaning does not require a new revision or blanket test reruns.

Local criteria describe the ticket's verifiable contribution and map to parent IDs. A ticket can complete with every local criterion `PASS` while a parent AC remains `PARTIAL` awaiting other tickets and integration. Only final acceptance can declare the whole spec delivered. Every parent AC must have delivery coverage; assigning it only to the final review is not implementation coverage.

## Execute and report

Before editing, reconcile the working set, local criteria, invariants, intended surface, exclusions, and ambiguities. In manual single-agent work this is an internal check, not a separate human approval gate. Finish with a concise report for the user. In orchestrated work, use the grounding mode explicitly assigned by the Lead under `/review-loop`: read-only grounding waits for authorization, while settled work may be preauthorized to ground then implement. Record the result either way; unresolved conflicts or material uncertainty return the affected work to the Lead.

Persist completion evidence in the ticket so another session can resume without conversation history:

| Criterion + revision | Status | Evidence and what it proves | Validated state |
| --- | --- | --- | --- |
| `LC-01 r1` → `AC-02 r1` | `PASS / FAIL / PARTIAL / UNKNOWN` | Exact test/manual check or inspected code/doc; command and exit status when run | Commit plus relevant uncommitted changes, if any |

Use `PASS` only for evidence that establishes the criterion. A test name or implementation assertion alone is insufficient. `FAIL` contradicts it; `PARTIAL` proves only a portion; `UNKNOWN` lacks usable proof. Required environmental/manual checks remain `UNKNOWN` when unavailable, even when that limitation was already reported. Known unavailability is not itself a code defect.

Record changed files, validation commands/results, accepted findings and fixes, remaining risks, and anything undone. Update the ticket's completion status only when its local criteria and required checks pass. Preserve pending parent ACs. Manual execution does not automatically start the next ticket or a review/fix loop; follow the user's invocation scope.

If later review disproves a reported `PASS`, link the correction and repair evidence back to that ticket's record so the old claim is no longer presented as current proof.

Reuse validation only while its exercised code, configuration, dependencies, and criterion meaning remain applicable. A later relevant edit invalidates the affected evidence, including when only assertions or expected values change. Re-run the affected checks; a new full suite is justified only when the required gate or affected scope warrants it. Never weaken a test to obtain a passing AC; distinguish a legitimate test correction from an approved requirement change.

## Stage-specific references

- When **creating/updating a spec, evidence pack, or tickets**, read the relevant sections of [Delivery Artifacts](delivery-artifacts.md).
- When **creating or executing the final review ticket**, read [Final Acceptance Review](final-acceptance-review.md).
- For **automatic implementation/review/fix orchestration**, invoke the installed `/review-loop` when requested. This protocol alone does not select a backend or authorize delegation.
