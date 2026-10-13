# Delivery Artifacts

Producer reference for [Delivery Context](delivery-context.md). Read only the section for the artifact being produced. Follow the target project's `docs/agents/issue-tracker.md`; keep existing `PRD.md` paths instead of creating duplicate specs.

## Spec

Extend the upstream spec shape with a small entry layer:

```markdown
## Delivery index

- Planning: <relative link>
- Evidence: <relative link to evidence/index.md, or explicitly none>
- Shared constraints: <section link; always read>
- Acceptance index: <AC ID, revision, one-line purpose, link to original section>

## Shared constraints

<Current cross-cutting obligations and required confirmed decision/ADR links.>

## Acceptance criteria

### AC-01

Revision: r1

Outcome: <observable outcome>

Requirement: <precise confirmed behavior, including relevant edge cases>

Verification: <what would establish or falsify this outcome>

Context: <required decision links; optional references with read conditions>
```

Keep ID-only AC headings (or explicit stable anchors) so title edits do not break inbound links. Keep the shared layer concise, but cover every obligation applicable across tickets. Detailed reasoning stays in planning/decision records, and reference excerpts stay in evidence. Link to original AC sections rather than copying their wording into several artifacts. Generation must check all confirmed planning requirements and decisions for coverage or explicit disposition; synthesis must not silently drop them or promote proposals.

For substantive revisions, append the user decision, superseded meaning, affected IDs/tickets, and evidence needing revalidation. Preserve existing links and status history. Assigning IDs to unchanged legacy criteria is a mechanical migration; resolving ambiguous criteria is a user decision. A missing evidence pack is not permission to invent evidence or require an otherwise unnecessary investigation.

## Evidence pack

Default location for a local Markdown tracker (use the project's configured evidence location when different):

```text
.scratch/<feature>/
  evidence/
    index.md
    decisions.md                 # only if task decisions need their own record
    references/<anchor-id>.md    # only for substantial retained excerpts/details
```

Reuse an existing task evidence/context location when one already serves this role. Use the project's domain glossary convention; `CONTEXT.md` is the default. Create only populated files; small related records can share a file and stable headings. The index is a navigation layer, not a second copy of planning/spec.

### Artifact lifetime and commit boundary

Apply the installed `task-artifacts` root rule for text-only evidence, temporary capture locations, explicit commit paths, and revision-scoped review records. This rule is a required companion to this protocol.

- After authorized cleanup, remove superseded task captures and retain their textual findings/provenance. Mark removed or temporary references honestly instead of leaving image embeds or links that imply current, committed evidence. Preserve unrelated and user-supplied design assets.

The index links the related planning/spec and lists each anchor's stable ID, short purpose, confirmation state, target section, and read condition. Suggested IDs: `DEC-*` decisions, `OBS-*` observations, `REF-*` references, `TRAP-*` pitfalls, `NEG-*` rejected examples. Required decisions/traps and optional reference details must be distinguishable. Keep IDs when records move and update their inbound links.

Each record carries:

- **Source:** original file/symbol, URL or conversation/decision reference; record the commit/version or observation date when available. Distinguish an inspected source from a recalled or unavailable one. For conversation-only material, retain the decision/observation itself and its confirmation basis; an inaccessible chat pointer alone is insufficient.
- **Confirmation:** `Confirmed`, `Proposed`, `Assumed`, `Open`, or `Rejected`, with the confirmation basis. Confirming an observation does not make it a product requirement.
- **Authority and scope:** `Canonical`, `Established Pattern`, `Reference Only`, `Observation Only`, or `Rejected / Negative Example`; explain the basis and the specific aspect it covers. External/temporary code defaults to non-authoritative.
- **Use / do not copy / uncertainty:** the useful behavior or pattern, excluded behavior, and anything not verified. Link approved obligations to their mandatory decision/spec entry.
- **Lifetime:** durable or ephemeral source; retained excerpt/artifact and capture status if needed.

For ephemeral sources, retain the minimum sufficient excerpt, surrounding assumptions, observation, or negative example **before removal**. A `/tmp` path alone is not durable evidence. Explain what the excerpt demonstrates and what it does not prove. Prefer relevant excerpts over whole implementations, but preserve enough context to avoid changing their meaning. If the source has already disappeared, record the gap rather than reconstructing a quotation from memory. Capturing evidence does not authorize deleting the original source.

Keep already valid records and explicitly supersede changed ones. Durable code pointers still need symbols/purpose and their known version; re-inspect changed code before treating it as current. Temp logs can support investigation, but durable handoff retains the necessary result/excerpt and provenance here rather than promising a worker access to a vanished log.

## Delivery ticket

Extend the upstream ticket shape. For a local Markdown tracker, retain one file per ticket; for a remote tracker, preserve the same fields in each issue:

```markdown
# <NN> — <Outcome>

Status: ready-for-agent

Type: delivery

Parent: <relative spec link>

Protocol: delivery-protocol — references/delivery-context.md
Protocol source: <toolkit repository and locked revision; project entry link if present>

Implements: AC-02 r1, AC-04 r1

Blocked by: <ticket links, or None>

## What to build

<Verifiable slice of behavior.>

## Working set

- Parent delivery index and shared constraints: <links>
- AC-02 / AC-04 original text: <section links>
- Required decisions/traps: <anchor links and reasons, or None>
- On demand: <reference links, useful aspect, and when to read; or None>

## Local acceptance

- [ ] LC-01 r1 → AC-02 r1: <this ticket's observable contribution>
- [ ] LC-02 r1 → AC-04 r1: <this ticket's observable contribution>

## Scope boundaries

<Relevant exclusions and invariants, linking their authoritative source.>

## Completion

<Persist the evidence table and execution summary from Delivery Context here. Identify partial parent coverage; complete local work does not close the spec.>
```

Pointers can contain paths and symbols; their purpose is grounding, not prescribing line-by-line edits. Keep full reference excerpts out of tickets. Common constraints cannot be omitted merely because they lack their own AC.

Before publishing, verify parent/anchor links, exact criterion IDs and revisions, every local-to-parent mapping, independently verifiable local completion, and dependency edges. Every parent AC needs at least one delivering ticket plus final verification. Preserve existing ticket IDs and completion evidence on updates; do not recreate the tree or renumber old tickets.

Create or update a single **final acceptance review ticket** after the delivery tickets, using [Final Acceptance Review](final-acceptance-review.md). It is a review task, not an extra implementation slice. It depends on all delivery tickets and carries the whole-spec coverage map. When extending an existing issue set, keep this dependency/coverage map current without assigning a new identity to the existing final ticket.
