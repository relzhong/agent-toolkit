---
root: true
targets:
  - '*'
---

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
