---
root: true
targets:
  - '*'
---

# Rulesync Authoring

In projects managed by Rulesync, treat the configured Rulesync input tree as the source of truth. The default source locations are `.rulesync/rules/` and `.rulesync/skills/`.

Edit source files, then run `rulesync generate` for the configured targets.

Treat generated tool directories as outputs. Edit them directly only when explicitly debugging generated output.

For remotely installed rules and skills, make shared changes in the owning source repository. Files under `.curated/` are installed artifacts.

When a project intentionally needs a local override, place it in the project's source tree and document why it differs from the shared version. Keep unrelated upstream content unchanged.
