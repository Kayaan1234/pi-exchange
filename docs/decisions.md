## 2026-11-19 — Folders named by role, not hardware**
Context: Decide where each Pi's code lives.
Options: Hardware-based (piA, piB) or role-based (exchange, generator).
Decision: exchange and generator.
Why: Directories represent software roles, not physical hardware. This allows boards to be replaced without restructuring the codebase.
Trade-off: Must map hardware to roles using SSH aliases.
Revisit if: Hardware identity becomes relevant to code organisation.

## 2026-11-19 — Ownership of shared code**
Context: Both the exchange and generator require shared code.
Options: Owned by exchange, generator, or a separate shared directory.
Decision: Exchange owns shared code.
Why: The exchange is authoritative, and the generator conforms to its interface.
Trade-off: The generator depends on exchange-owned code. Limit coupling by sharing only what is necessary.
Revisit if: Shared code becomes independent of the exchange or generator requirements diverge.

## 2026-11-19 — Test directory structure**
Context: Decide where unit and end-to-end tests live.
Options: One central tests directory or component-specific test directories.
Decision: exchange/tests and generator/tests for component tests; root tests/ for end-to-end tests.
Why: Each component owns its tests, while cross-component tests remain separate.
Trade-off: Tests are spread across multiple directories, requiring coordinated execution.
Revisit if: Test duplication or execution complexity becomes significant.

## 2026-11-19 — Build on each target Pi
Context: Decide where each component is compiled.
Options: Build centrally and transfer binaries, or compile on each Pi.
Decision: Build each component on its target Pi, synchronise with Git, and record commit hashes.
Why: Avoids cross-compilation complexity and ensures source revisions are traceable.
Trade-off: Two build environments must be maintained and synchronised.
Revisit if: Build times or environment inconsistencies become problematic.

## 2026-11-19 — Enforce compiler warnings
Context: Default GCC settings failed to detect common programming mistakes.
Options: Default warnings, targeted flags, or broader warning coverage.
Decision: Use `-Wall -Wextra -Wconversion -Werror`.
Why: Tests confirmed these flags detect unused variables, signed/unsigned comparisons, and lossy conversions.
Trade-off: Strict warnings may require additional fixes.
Revisit if: Compiler upgrades or dependencies introduce excessive warnings.

## 2026-11-19 — Separate debug and measurement builds
Context: Debugging and benchmarking require different compiler settings.
Options: Single build configuration or separate debug and measurement builds.
Decision: Use `-O0 -g` for debugging and `-O2` for measurement, retaining `-Werror` in both.
Why: Debug builds prioritise inspectability; optimised builds provide representative performance measurements.
Trade-off: Two configurations require maintenance.
Revisit if: Profiling reveals the need for different optimisation settings.