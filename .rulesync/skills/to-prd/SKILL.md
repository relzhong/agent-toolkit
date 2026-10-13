---
name: to-prd
description: >-
  Publish or update a repo spec through upstream to-spec, preserving confirmed planning decisions, stable acceptance IDs, and layered evidence links. Use /to-prd for the enhanced specification workflow.
targets:
  - '*'
---

# To PRD

Load the installed `delivery-protocol` skill once before using the shared references below. Resolve project paths from the target project root, not this skill directory. If a sibling skill link is unavailable, locate that installed skill by name; do not assume an agent-specific installation path.

Use the installed upstream `to-spec` process and template with the local additions below. Read that skill's instructions explicitly; this wrapper is the requested entry, not a request to modify or recursively route back from upstream. Preserve the upstream source unchanged.

## Prepare

1. Read `docs/agents/issue-tracker.md`, [delivery-context](../delivery-protocol/references/delivery-context.md), and the **Spec** section of [delivery-artifacts](../delivery-protocol/references/delivery-artifacts.md).
2. Locate the existing spec (`PRD.md` remains valid), related planning, and any evidence index. Reconcile the confirmed discussion with planning; when decisions or temporary references need capture, use the installed `evidence-pack` skill before publishing. Existing evidence is optional; unavailable sources remain explicit gaps, not invented facts.
3. Follow upstream exploration and testing-seam decisions. Reuse already-approved seams and requirements without asking again; ask only for a still-unresolved decision. Check confirmed requirements, constraints, reasons, rejected approaches, and known edge cases for a durable destination. Keep proposals and open matters distinct from requirements.

## Extend before publishing

Add the delivery index, mandatory shared constraints, stable revisioned parent ACs, and links to their planning/evidence basis. Preserve prior IDs, paths, and history on updates. For changed meaning, apply the user-approved revision and invalidate affected evidence according to Delivery Context; never waive acceptance as an implementation convenience.

**Local exception to upstream's path/snippet restriction:** allow source links, symbols, decision/AC anchors, and small decision-rich snippets where needed for fidelity. Keep reference implementations and detailed excerpts behind evidence pointers, with scope and read conditions; tickets/specs do not become line-by-line edit scripts.

Before the upstream publish/ready step, verify every confirmed requirement and constraint is represented or explicitly superseded/deferred by an approved decision; every AC is observable and uniquely identified; required links resolve; and unresolved blockers are visible. A spec with unresolved requirement blockers is not `ready-for-agent`. Use `needs-info` for the affected artifact and explain the missing decision while continuing unaffected work. Ordinary implementation details do not need approval.

## Finish

Report the spec path, AC IDs/revisions, evidence entry, preserved/superseded decisions, and unresolved items. On updates, list affected tickets and validation needing renewal without silently rewriting their completed claims. Recommend the installed `/to-issues` wrapper for decomposition.
