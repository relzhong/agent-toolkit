---
root: true
targets:
  - '*'
---

# Task Artifacts

Skill packages contain reusable instructions, scripts, and intentional reusable assets.

Keep task-specific screenshots, recordings, logs, browser dumps, generated mockups, and exports in a task-specific temporary directory, outside skill packages and evidence packs. Use `/tmp` where available, or the operating system's temporary directory.

Evidence packs contain text only: decisions, observations, relevant excerpts, commands and results, tested revisions, provenance, and known limits.

Deliberately maintained product or design assets belong in their approved source locations.

When preparing a commit, select explicit source, test, and documentation paths. Raw task captures are not commit material.

Past review records describe their tested revision. Inspect the current implementation and maintained design documents before treating a past review as evidence of the current product state.

For tasks using the enhanced delivery workflow, read the Artifact lifetime and commit boundary section of the installed `delivery-protocol` skill's `references/delivery-artifacts.md` for capture cleanup and provenance handling.
