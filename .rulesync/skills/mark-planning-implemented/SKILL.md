---
name: mark-planning-implemented
description: >-
  Add a final implemented status note to an existing `docs/planning/` markdown file after the planned work has shipped. Use when the user wants to mark a planning doc as finished, move it to the implemented planning archive, and link to the resulting GitHub issues, ADRs, concept docs, PRs, or other durable implementation references.
---

# Mark Planning Implemented

## Purpose

Close the loop on a planning document by preserving the original plan as historical intent in `docs/planning/implemented/`. Add a short status block at the top that points future readers to the implemented work and current system contracts, and keep `docs/planning/implemented.md` as the discoverable archive index.

Use this after implementation, issue closure, and any architecture/concept documentation are complete.

Project paths in this skill are defaults. Follow the target project's established planning, glossary, ADR, concept, and archive locations when they differ; resolve paths from that project root. Missing optional domain documents do not block planning.

## Workflow

1. Read the target planning file under `docs/planning/`.
2. Inspect nearby implementation context only as needed: linked issues, git history, PR notes, ADRs, `docs/concepts/`, and `CONTEXT.md`.
3. Identify the smallest useful set of finished-work links:
   - GitHub issues or PRs that delivered the plan.
   - ADRs that record accepted decisions.
   - Concept docs that describe current behavior.
   - `CONTEXT.md` when shared language changed.
4. Add or update a status block immediately below the planning document title.
5. Preserve the original planning content except for marking completed vertical slices when that is clearly supported by the implementation record.
6. Move the implemented planning file from `docs/planning/<name>.md` to `docs/planning/implemented/<name>.md`.
7. Fix relative links inside the moved planning file for the new depth:
   - `../adr/` becomes `../../adr/`.
   - `../concepts/` becomes `../../concepts/`.
   - `../../CONTEXT.md` becomes `../../../CONTEXT.md`.
   - `../../.scratch/` becomes `../../../.scratch/`.
   - `../../packages/` becomes `../../../packages/`.
8. Update repository links that pointed at the old planning path, including concept docs, ADRs, `.scratch` issue files, and other planning docs. Point them at `docs/planning/implemented/<name>.md` or the correct relative path.
9. Add or update `docs/planning/implemented.md` with a short entry for the archived plan. If the file does not exist, create it as the archive index.
10. Summarize the planning file moved, the archive index entry, and the implementation references added or updated.

## Status Block Shape

Use this shape directly below the top-level heading:

```markdown
> Status: Implemented
>
> Finished work:
>
> - GitHub Issue: <url or #number>
> - ADR: <relative link>
> - Concept: <relative link>
> - Shared language: <relative link>
```

Omit link categories that do not apply. Keep link labels stable and concrete: `GitHub Issue`, `Pull Request`, `ADR`, `Concept`, and `Shared language`.

If there is already a status block, update it instead of adding a second one. If the plan is only partially implemented, write `Status: Partially Implemented` and list only the shipped references.

## Writing Rules

- Prefer present-tense outcome language over implementation chronology.
- Do not convert the planning doc into an ADR or concept doc.
- Do not add speculative future work to the implemented status block.
- Keep the block short enough that the original planning title and intent remain easy to scan.
- Use relative links for repo docs and full URLs for external GitHub resources.
- Keep active, partially implemented, or unimplemented plans in `docs/planning/`. Only move plans whose shipped references support `Status: Implemented`.
- Keep `docs/planning/implemented.md` short: one entry per archived plan with a one-sentence description of the shipped outcome.
- After moving a plan, search for stale references to the old `docs/planning/<name>.md` path and update them.

## Handoff

After editing, mention that the planning document now lives in `docs/planning/implemented/` as historical intent, `docs/planning/implemented.md` indexes it, and ADRs, concept docs, and `CONTEXT.md` hold the current durable contract.
