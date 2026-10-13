---
root: true
targets:
  - '*'
---

# Tool Output

Keep tool output context-efficient while preserving necessary verification and failure evidence.

- Prefer concise modes and targeted reads/searches. For large output, save the complete permitted output to a task-specific temporary file, then inspect relevant sections.
- Report the original command's exit status, a result summary, and sufficient failure context. Filtering and pipelines must not mask the original command's failure.
- State when output is filtered or truncated. Expand the inspected context when the available evidence cannot support the decision.
- Do not dump large logs, generated files, dependency trees, or full test output into context by default.
- Keep potentially useful full output on disk and report its path.
- When the task requires durable evidence, retain the relevant commands, results, failure excerpts, and provenance in its existing evidence record. Temporary log paths alone are not durable evidence.
