---
name: to-issues
description: >-
  Decompose a repo spec through upstream to-tickets, adding parent AC mappings, layered context pointers, local acceptance, and a final whole-spec review ticket. Use /to-issues for the enhanced ticket workflow.
targets:
  - '*'
---

# To Issues

Load the installed `delivery-protocol` skill once before using the shared references below. Resolve project paths from the target project root, not this skill directory. If a sibling skill link is unavailable, locate that installed skill by name; do not assume an agent-specific installation path.

Read and follow the installed upstream `to-tickets` process for tracer-bullet slicing, dependencies, and publishing, with these local additions. Preserve its source unchanged; invoke its instructions within this wrapper rather than delegating away the completion checks.

## Establish the parent

Read `docs/agents/issue-tracker.md`, [delivery-context](../delivery-protocol/references/delivery-context.md), and the **Delivery ticket** section of [delivery-artifacts](../delivery-protocol/references/delivery-artifacts.md). Read the source spec and relevant planning/evidence in sections so all requirements are covered during decomposition without copying the entire context into each ticket.

Enhanced tickets require a canonical parent with stable AC IDs/revisions and shared constraints. If given only a plan/conversation, or a legacy spec missing that contract, report the precise gap and route preparation to `/to-prd`. Do not silently invent a canonical parent or edit it from this stage. Continue preparing unaffected draft slices, but publish the enhanced set only after the parent contract is available. Respect already-approved breakdowns instead of repeating the upstream interview.

## Extend the breakdown

- Preserve upstream vertical slices and its expand/contract exception for wide refactors. Each delivery ticket has independently verifiable local criteria mapped to its contribution to parent ACs.
- Include parent/protocol links, required shared constraints and original AC sections, selected mandatory decisions/traps, and optional references with specific read conditions. Reference code normally belongs at the deepest, on-demand level.
- Carry existing evidence anchor IDs forward. If decomposition discovers important observations or temporary references, retain them using the Evidence pack section of Delivery Artifacts (or `/evidence-pack` when planning also needs synchronization) before publishing their links. Keep new suggestions unconfirmed and leave canonical requirement changes to `/to-prd`.
- **Local exception to upstream's path/snippet restriction:** source links, symbols, and evidence anchors are permitted for grounding. Link detailed excerpts rather than reproducing implementations or prescribing line edits.
- Add one final acceptance review ticket depending on all delivery tickets, following [final-acceptance-review](../delivery-protocol/references/final-acceptance-review.md). Show this task in the proposed breakdown; it verifies whole-spec coverage and integrated behavior, not just ticket status. It does not require an Architecture Review for every change.
- Keep existing ticket identities and final-review links on updates. Any new delivery work must be reflected in the final ticket's dependencies and coverage map.

## Validate before publishing

Check the approved breakdown for every parent AC's delivering ticket(s), exact ID/revision mappings, measurable local acceptance, reachable required context, explicit exclusions, and an acyclic dependency graph. Include shared constraints even when not expressed as separate ACs. The final ticket must cover all current parent ACs and carry or request the real feature review base before implementation begins.

Publish one artifact per approved ticket in the configured tracker using the enriched shape from Delivery Artifacts (one file per ticket for a local Markdown tracker). Include the durable execution/reporting protocol so manually executed tickets work without `/review-loop`. Do not mark an implementation ticket incomplete solely because its parent AC still awaits another ticket; do not close the parent because implementation tickets alone are complete.

## Finish

Report ticket paths, dependencies, parent-AC coverage, final-review ticket, and unresolved gaps. Users may execute tickets manually or request `/review-loop`; this publishing step starts neither mode automatically.
