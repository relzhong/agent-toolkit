---
root: true
targets:
  - '*'
---

# Coding Discipline

Keep modules deep, state authoritative in one place, and validation proportional to real trust boundaries. Simplify by deleting duplicate responsibility, never by moving it into a heavier generic framework.

- **Validate once at trust boundaries**: Perform input, configuration, credential-reference, and artifact admission once at the ingestion or lock-acquisition boundary, then pass typed validated values internally. Acquire the lock before reading authoritative local state once rather than reading pre-lock and comparing fields later. Under an active local lock, trust in-memory state and atomic writes rather than re-reading, re-hashing, or re-parsing unchanged local files; keep runtime checks for external state (processes, remote nodes, cluster or database state) that local locks do not govern.
- **Single authority without speculative abstraction**: Keep one authoritative durable record per workflow; secondary scheduling, cache, or UI views must project state rather than duplicate state machines or invariant checks. Share small execution helpers across similar steps, but do not force distinct domain protocols into a generic table-driven engine, replace typed fields with untyped string maps, or bundle persistence-schema rewrites into feature work.
- **Bounded recovery and stop-on-uncertain-effect**: Converge an interrupted step from live observation only when its complete terminal state is observable. For non-idempotent mutations where service health cannot prove data completeness (such as partial data imports or migrations), preserve state and halt for intervention rather than guessing or building auto-repair engines. Do not leave incident-specific recovery branches, historical hash allowlists, or one-off salvage commands in production code.
- **Safe, actionable diagnostics**: Surface existing non-secret diagnostic context (exit status, error codes, failing step, source location) in error messages. Redact secret values at the boundary without masking failure causes behind opaque fallback strings, dumping raw secret-bearing output, or inventing a new diagnostic framework.
- **Behavioral tests over structural lock-in**: Test observable behavior, state transitions, and error contracts through module interfaces. Do not write tests that grep source files for literal substrings, enforce AST naming prefixes, lock down non-contractual task/log labels, or test local test-only helper predicates in isolation.
