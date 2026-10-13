---
name: delivery-protocol
description: Shared acceptance and evidence protocol for to-prd, to-issues, evidence-pack, review-loop, and manual execution of tickets declaring this protocol. Load when one of these workflows needs its shared contract; ordinary direct tasks do not require a spec or ticket tree.
---

# Delivery Protocol

This skill owns shared delivery requirements, artifact shapes, and final acceptance. It does not start publishing, implementation, or delegation.

## Project bindings

Resolve all project paths against the target project root. Paths under this skill's `references/` are package resources, not project output locations.

- Read the project's `docs/agents/issue-tracker.md` when locating or publishing specs, tickets, and evidence. Use its existing tracker, paths, and identities. If missing, use an explicit tracker choice already supplied by the user; otherwise ask only for the missing destination before publishing. The installed `setup-matt-pocock-skills` skill can establish these conventions when requested.
- Read `docs/agents/domain.md` and `docs/agents/triage-labels.md` when relevant and present. Honor the project glossary and label mappings. The toolkit's default examples use `CONTEXT.md`, `docs/planning/`, `docs/adr/`, and `docs/concepts/`; established project conventions take precedence over these paths.
- The `task-artifacts` root rule is a required companion for evidence lifetime and commit boundaries. If absent from the loaded project instructions, locate and read the installed Rulesync rule by that name before creating evidence. If unavailable, report the missing dependency rather than inventing an artifact policy.
- Existing project protocol entry files may link here. Preserve their stable paths and section anchors when migrating projects; do not duplicate the shared protocol body there.

## Read by stage

1. Read [Delivery Context](references/delivery-context.md) for responsibilities, mandatory context, stable criterion revisions, execution, and completion evidence.
2. When producing a spec, evidence pack, or ticket, read only the corresponding section of [Delivery Artifacts](references/delivery-artifacts.md).
3. When creating or executing the final review ticket, also read [Final Acceptance Review](references/final-acceptance-review.md).

Published tickets identify this skill, the relevant reference, and the toolkit revision locked by the consuming project (or another pinned source revision). Prefer an existing stable project protocol entry for repository links. Do not put a machine-specific installation path in a durable ticket.

If a required reference or dependency is unavailable, report its exact name and continue only unaffected preparation. Project instructions and confirmed user decisions remain authoritative; references do not create new scope or authorize weaker acceptance.
