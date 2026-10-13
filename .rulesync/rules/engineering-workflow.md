---
root: true
targets:
  - '*'
---

# Engineering Workflow

Use the stages relevant to the user's request. Existing approved plans and decisions remain valid; do not restart completed stages or manufacture a specification and ticket tree for a small direct task.

Resolve documentation locations using the target project's established conventions. The paths below are defaults, not a requirement to relocate an existing documentation tree.

## Planning

For a raw feature idea, use `sketch-to-planning`.

For a conversation that already contains implementation decisions, constraints, rationale, or rejected alternatives, use `conversation-to-planning`.

For an architecture review finding or refactoring proposal, use `review-to-planning`.

When stress-testing a plan, use `grill-with-docs` against the project's current implementation, domain documentation, and applicable ADRs.

Capture resolved domain language in the project's glossary. Create ADRs for meaningful architectural decisions rather than routine details.

## Evidence and Specification

After discussion resolves decisions, use `evidence-pack` to synchronize planning and retain scoped evidence. It may also run earlier when temporary references could disappear.

Use `to-prd` to publish or update the enhanced specification, and `to-issues` to decompose it into delivery tickets.

These wrappers extend the installed `to-spec` and `to-tickets` skills. Invoking the upstream skills directly does not apply those extensions.

For tasks declaring the enhanced delivery protocol, load the installed `delivery-protocol` skill. Read mandatory requirements and decisions first; load reference details when needed.

## Execution and Review

Manual ticket execution records durable completion evidence without a separate grounding approval step.

The final acceptance review verifies the whole specification before closure. Completing implementation tickets alone does not close the spec.

Use `review-loop` when the user requests implementation/review/fix orchestration. Use `review-loop-paseo` when Paseo orchestration is requested.

For codebase architecture diagnostics and refactoring opportunities, use `improve-codebase-architecture`. Save its HTML reports under `docs/review/`.

## Completion

After TDD work completes, mark the related issue closed or implemented when one exists, and record delivered scope and verification commands.

After implementation or refactoring, use `document-architecture-decision` to capture useful finalized knowledge. Record consequential decisions in ADRs, domain language in the glossary, and explanatory implementation knowledge in concept documentation.

When all work in a planning document has shipped and required acceptance is complete, use `mark-planning-implemented` to record delivery links and archive the completed plan.
