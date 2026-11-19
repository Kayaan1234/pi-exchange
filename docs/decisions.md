**## 2026-11-19 — Folders named by role, not hardware**
Context: Decide where each Pi's code lives.
Options: Hardware-based (piA, piB) or role-based (exchange, generator).
Decision: exchange and generator.
Why: Directories represent software roles, not physical hardware. This allows boards to be replaced without restructuring the codebase.
Trade-off: Must map hardware to roles using SSH aliases.
Revisit if: Hardware identity becomes relevant to code organisation.

**## 2026-11-19 — Ownership of shared code**
Context: Both the exchange and generator require shared code.
Options: Owned by exchange, generator, or a separate shared directory.
Decision: Exchange owns shared code.
Why: The exchange is authoritative, and the generator conforms to its interface.
Trade-off: The generator depends on exchange-owned code. Limit coupling by sharing only what is necessary.
Revisit if: Shared code becomes independent of the exchange or generator requirements diverge.

**## 2026-11-19 — Test directory structure**
Context: Decide where unit and end-to-end tests live.
Options: One central tests directory or component-specific test directories.
Decision: exchange/tests and generator/tests for component tests; root tests/ for end-to-end tests.
Why: Each component owns its tests, while cross-component tests remain separate.
Trade-off: Tests are spread across multiple directories, requiring coordinated execution.
Revisit if: Test duplication or execution complexity becomes significant.