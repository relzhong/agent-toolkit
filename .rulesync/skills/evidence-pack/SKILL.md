---
name: evidence-pack
description: >-
  Sync confirmed discussion decisions back into planning and retain a layered evidence index, scoped references, and temporary-code excerpts for later spec, ticket, and implementation handoffs. Use after grilling or investigation, or before temporary reference material or session context is lost.
targets:
  - '*'
---

# Evidence Pack

Load the installed `delivery-protocol` skill once before using the shared references below. Resolve project paths from the target project root, not this skill directory. If a sibling skill link is unavailable, locate that installed skill by name; do not assume an agent-specific installation path.

Preserve decision provenance and useful evidence without turning reference code into requirements. This skill produces planning/evidence artifacts, not specs, tickets, production changes, or source deletion.

## Workflow

1. Locate the task's existing planning and feature directory using `docs/agents/issue-tracker.md`. Read [delivery-context](../delivery-protocol/references/delivery-context.md) and the **Evidence pack** section of [delivery-artifacts](../delivery-protocol/references/delivery-artifacts.md), including its artifact lifetime and commit boundary. Resolve output paths from the project root, never the skill directory. Reuse existing artifact identities and locations.
2. Read and follow the installed `conversation-to-planning` skill to update the existing plan from the relevant discussion, or create one if absent. Preserve confirmed, proposed, assumed, open, and rejected states. Capture user-confirmed changes and supersede old decisions; do not re-interview settled matters or declare proposals approved. This synchronizes planning; it does not silently update an already-published canonical spec. Report affected spec/AC revisions for the next `/to-prd` pass.
3. Build or update the small evidence index and only the needed deeper records. Link existing decisions/ADRs instead of copying them. Capture source/version, confirmation basis, trusted scope, uncertainty, excluded patterns, and read conditions. Promote already-approved invariants into the mandatory planning/decision layer while leaving reference code on demand.
4. For temporary references, inspect them while available and retain minimum sufficient text excerpts/observations before removal. The pack is text-only: record findings, provenance, commands/results and tested revisions, not screenshots, recordings, browser dumps or generated exports. Keep raw validation captures in a task-specific `/tmp` directory outside the pack; a skill directory is reusable source, not task output. Mark missing sources honestly. Do not archive whole implementations by default or infer trust from completeness. Running this skill early is allowed; later runs update the same pack.
5. Check coverage against the discussion and inspected material: every consequential decision, rejected approach, trap, and selected reference has a durable text record or an explicit unresolved/deferred disposition. Verify links and that ephemeral sources have content beyond their paths. After authorized cleanup, mark captures as removed; preserve their observations instead of broken links. A past review describes its tested revision, not the current product. Preserve substantive information in deeper sections rather than deleting it to shorten the index.

## Finish

Report the updated plan, evidence index, relevant anchor IDs, unresolved gaps, and any published spec/tickets requiring synchronization. Recommend `/to-prd` when requirements are ready to publish, then `/to-issues`; do not invoke those publishing stages merely because evidence capture completed.
