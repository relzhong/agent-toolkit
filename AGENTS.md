# Additional Conventions Beyond the Built-in Functions

As this project's AI coding tool, you must follow the additional conventions below, in addition to the built-in functions.

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

Use `review-loop` for requested implementation/review/fix work: the current executor owns the work unit and calls fresh-context reviewers. Ordinary units default to one reviewer; automatic whole-spec acceptance defaults to `parallel-review`. Manual execution leaves parallel review to the user's choice.

Use `review-loop-paseo` only for requested Paseo orchestration. Its thin Lead dispatches issues and tracks completion; Implementation runs the shared loop. Delivery protocols define evidence and acceptance, not these agent roles.

For codebase architecture diagnostics and refactoring opportunities, use `improve-codebase-architecture`. Save its HTML reports under `docs/review/`.

## Completion

After TDD work completes, mark the related issue closed or implemented when one exists, and record delivered scope and verification commands.

After implementation or refactoring, use `document-architecture-decision` to capture useful finalized knowledge. Record consequential decisions in ADRs, domain language in the glossary, and explanatory implementation knowledge in concept documentation.

When all work in a planning document has shipped and required acceptance is complete, use `mark-planning-implemented` to record delivery links and archive the completed plan.

# Implementation Scope

Treat the task and approved plan as the scope contract.

- Resolve ordinary implementation details using existing project conventions and the approved design.
- Do not turn discovered concerns into new requirements.
- New configuration, environment variables, feature flags, policy layers, dependencies, or extension mechanisms must be justified by an explicit requirement or an established project convention. A hypothetical benefit alone is insufficient.
- Preserve the project's existing trust boundaries and configuration model unless the task explicitly changes them.
- If a concern can be resolved within the approved design, resolve it.
- If resolving a concern requires a material design or scope change, explain the concrete trigger, impact, and smallest proposed change before implementing that change.
- Continue unaffected work. If the concern invalidates the planned behavior or a key assumption, pause only the affected part.
- Report optional improvements separately; do not implement them without authorization.

Implementation may fill in details, but must not silently expand scope.

# Rulesync Authoring

In projects managed by Rulesync, treat the configured Rulesync input tree as the source of truth. The default source locations are `.rulesync/rules/` and `.rulesync/skills/`.

Edit source files, then run `rulesync generate` for the configured targets.

Treat generated tool directories as outputs. Edit them directly only when explicitly debugging generated output.

For remotely installed rules and skills, make shared changes in the owning source repository. Files under `.curated/` are installed artifacts.

When a project intentionally needs a local override, place it in the project's source tree and document why it differs from the shared version. Keep unrelated upstream content unchanged.

# Task Artifacts

Skill packages contain reusable instructions, scripts, and intentional reusable assets.

Keep task-specific screenshots, recordings, logs, browser dumps, generated mockups, and exports in a task-specific temporary directory, outside skill packages and evidence packs. Use `/tmp` where available, or the operating system's temporary directory.

Evidence packs contain text only: decisions, observations, relevant excerpts, commands and results, tested revisions, provenance, and known limits.

Deliberately maintained product or design assets belong in their approved source locations.

When preparing a commit, select explicit source, test, and documentation paths. Raw task captures are not commit material.

Past review records describe their tested revision. Inspect the current implementation and maintained design documents before treating a past review as evidence of the current product state.

For tasks using the enhanced delivery workflow, read the Artifact lifetime and commit boundary section of the installed `delivery-protocol` skill's `references/delivery-artifacts.md` for capture cleanup and provenance handling.

# Tool Output

Keep tool output context-efficient while preserving necessary verification and failure evidence.

- Prefer concise modes and targeted reads/searches. For large output, save the complete permitted output to a task-specific temporary file, then inspect relevant sections.
- Report the original command's exit status, a result summary, and sufficient failure context. Filtering and pipelines must not mask the original command's failure.
- State when output is filtered or truncated. Expand the inspected context when the available evidence cannot support the decision.
- Do not dump large logs, generated files, dependency trees, or full test output into context by default.
- Keep potentially useful full output on disk and report its path.
- When the task requires durable evidence, retain the relevant commands, results, failure excerpts, and provenance in its existing evidence record. Temporary log paths alone are not durable evidence.
