## 2026-11-19 — Folders named by role, not hardware
Context: Decide where each Pi's code lives.
Options: Hardware-based (piA, piB) or role-based (exchange, generator).
Decision: exchange and generator.
Why: Directories represent software roles, not physical hardware. This allows boards to be replaced without restructuring the codebase.
Trade-off: Must map hardware to roles using SSH aliases.
Revisit if: Hardware identity becomes relevant to code organisation.

## 2026-11-19 — Ownership of shared code
Context: Both the exchange and generator require shared code.
Options: Owned by exchange, generator, or a separate shared directory.
Decision: Exchange owns shared code.
Why: The exchange is authoritative, and the generator conforms to its interface.
Trade-off: The generator depends on exchange-owned code. Limit coupling by sharing only what is necessary.
Revisit if: Shared code becomes independent of the exchange or generator requirements diverge.

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

## 2026-11-22 — Dedicated Ethernet subnet
Context: The Pis need a communication path for the experiment that is separate from household Wi-Fi.
Options: Put `eth0` on the Wi-Fi subnet (`192.168.1.0/24`), or use a separate, non-overlapping subnet.
Decision: Use `10.10.10.0/24` for the cable, with Pi A (exchange) at `10.10.10.1` and Pi B (generator) at `10.10.10.2`.
Why: Overlapping connected networks on `wlan0` and `eth0` would create competing routes; a separate subnet makes the path easy to predict and inspect with `ip route get`.
Trade-off: Two address spaces to keep track of; the specific `10.10.10.x` values give no performance benefit.
Revisit if: A switch or additional devices join the experiment network.

## 2026-11-22 — Static addressing on Ethernet
Context: The exchange client needs a fixed destination, and benchmark runs must be comparable.
Options: DHCP, link-local auto-addressing, or manually assigned static addresses.
Decision: `ipv4.method manual` with fixed addresses on both Pis.
Why: There is no DHCP server on the direct cable, and fixed addresses remove a source of variation between runs.
Trade-off: Addresses must be managed by hand on each Pi.
Revisit if: The number of devices makes manual assignment error-prone.

## 2026-11-22 — Persist configuration through NetworkManager
Context: An address added with `ip addr add` disappeared, sending traffic back over Wi-Fi.
Options: Re-run `ip addr add` each session, use a startup script, or configure the NetworkManager profile.
Decision: Modify `Wired connection 1` on each Pi with `connection.autoconnect yes` and static IPv4 settings.
Why: NetworkManager is already managing the interfaces, and a saved profile is restored automatically after reboot.
Trade-off: Configuration is tied to NetworkManager; changes must go through `nmcli` rather than `ip`.
Revisit if: NetworkManager is replaced or interferes with the experiment.

## 2026-11-22 — Keep Wi-Fi as the default route
Context: Adding an Ethernet address could make the cable a candidate default route.
Options: Allow Ethernet to become the default route, or restrict it to the `10.10.10.0/24` traffic.
Decision: Set `ipv4.never-default yes` on both Ethernet profiles.
Why: The cable links only the two Pis; neither forwards traffic to the internet, so internet traffic must stay on Wi-Fi.
Trade-off: None significant for this project; the cable can't be used for any other destinations.
Revisit if: One Pi is deliberately configured as a gateway for the other.

## 2026-11-22 — Standard network verification sequence
Context: Several conclusions earlier in the project were drawn from a single command's output.
Options: Spot-check with `ping`, or run a fixed sequence covering each layer.
Decision: Before experiments, run `ip -4 addr show dev eth0`, `ip -4 route`, `ip route get <peer>`, `ip neigh show dev eth0`, and `ping -c 3 <peer>` on both Pis, and save the output with the run.
Why: Each command checks a different layer: address, routes, route choice, neighbour resolution and delivery. Saving the output means the evidence can be shown later, not just reported.
Trade-off: Takes slightly longer before each session.
Revisit if: The checks are automated in a pre-run script.

## 2026-11-22 — Correctness before measurement
Context: Discussion drifted into concurrency and optimisation before the exchange existed.
Options: Design for concurrency and latency from the start, or first get one order round-trip working.
Decision: First implement one order Pi B → Pi A → result → Pi B, and only instrument it once it is correct and repeatable.
Why: Optimising or measuring a transaction that isn't yet correct gives meaningless numbers.
Trade-off: Performance-driven design choices are postponed and may require later refactoring.
Revisit if: The single-transaction design blocks reasonable concurrency later.

## 2026-11-22 — Measure application latency, not ping
Context: Ping gave a ~0.32 ms RTT over the cable.
Options: Use ping as the latency baseline, or measure the exchange's own order round trip.
Decision: Treat ping only as a connectivity check; measure the latency distribution at the application level, and confirm with `tcpdump` on `eth0` that the order traffic uses the cable.
Why: ICMP doesn't go through the exchange's protocol, serialisation, scheduling or processing path, and three samples don't make a distribution.
Trade-off: Needs custom timestamping and many more runs.
Revisit if: Ping later becomes useful as a baseline for separating network cost from application cost.

## 2026-11-23 — Provisionally kept 4GB Pi as exchange, 8GB Pi as generator
Context: Decide which Pi runs the matching engine and which runs the load generator.
Options: 4GB exchange / 8GB generator, or the reverse.
Decision: 4GB for the exchange, 8GB for the generator, provisionally.
Why: `lscpu` showed identical CPUs, and the 4GB Pi has ~2.9 GiB available against a hypothetical ~200 MiB engine. One million 8-byte latency measurements is only ~8 MB, so the generator doesn't need 8GB either. Nothing measured favours reversing.
Trade-off: Neither choice is benchmarked, and the 200 MiB figure is hypothetical. The 4GB Pi has less margin if the engine grows.
Revisit if: Measured memory approaches the 4GB Pi's available RAM, or controlled benchmarks show a meaningful difference between the two roles.