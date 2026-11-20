## 2026-11-19 — Default compiler warnings were insufficient
What I believed / predicted: GCC would detect common programming mistakes by default.
What actually happened: Unused variables and signed/unsigned comparisons compiled silently until warning flags were enabled.
Why: GCC does not enable all available diagnostics by default.
What I'll do differently: Explicitly enable and test warning flags from the beginning.

## 2026-11-19 — Misunderstood signed/unsigned conversion
What I believed / predicted: Comparing `int(-1)` with `size_t(3)` would evaluate to false.
What actually happened: The comparison evaluated to true because `-1` converted to the maximum `size_t` value.
Why: I misunderstood the usual arithmetic conversion rules.
What I'll do differently: Avoid mixed-sign comparisons and verify implicit conversions.

## 2026-11-19 — Confused warnings with runtime overhead
What I believed / predicted: Removing compiler warning flags would improve measurement builds.
What actually happened: `cmp` showed identical binaries with and without the tested warning flags.
Why: Compiler diagnostics do not necessarily affect generated machine code.
What I'll do differently: Keep warnings enabled and measure actual optimisation effects.

## 2026-11-19 — Misattributed binary size reduction
What I believed / predicted: Enabling `-O2` reduced the executable's size.
What actually happened: Removing `-g` explained the reduction; I had changed multiple flags simultaneously.
Why: I attributed an observed result to one variable without isolating it.
What I'll do differently: Change one compiler setting at a time when investigating build differences.

## 2026-11-19 — Confused compilation and runtime constraints
What I believed / predicted: Compiling on the 4GB Pi would guarantee compatibility with the 8GB Pi.
What actually happened: RAM capacity alone does not determine binary compatibility.
Why: I confused runtime memory constraints with compilation and platform compatibility.
What I'll do differently: Build on each target device and verify compatibility independently.

## 2026-11-20 — Failed to isolate build configurations
What I believed / predicted: Separate build directories for exchange and generator were sufficient to organise Debug and Measure builds.
What actually happened: I initially created one directory per component, then realised each component needed separate Debug and Measure directories.
Why: I organised builds by component but overlooked the configuration dimension.
What I'll do differently: Define build directories by both component and configuration before running CMake.