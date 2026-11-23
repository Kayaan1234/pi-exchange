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

## 2026-11-22 — Confused route selection with reachability
What I believed / predicted: `ip route get 10.10.10.2` would select Wi-Fi because Pi B had not yet been configured with that address.
What actually happened: Linux selected `eth0`, because the connected `10.10.10.0/24` route matched regardless of whether Pi B existed.
Why: A routing lookup decides how Linux would attempt to reach a destination; it does not check that the destination is present.
What I'll do differently: Treat route selection and delivery as separate questions, and test each with the appropriate command.

## 2026-11-22 — Invented a cause for the disappearing Ethernet address
What I believed / predicted: When `ip route get` returned Wi-Fi again, I assumed Linux had searched for Pi B, failed, and fallen back to the default route.
What actually happened: `ip -4 addr` and `ip -4 route` showed the temporary `eth0` address and its connected route had been removed.
Why: `ip addr add` only changes the running kernel state; it is not saved configuration and can be lost on reboot or by network-management activity.
What I'll do differently: Inspect the current state before explaining unexpected behaviour, and use persistent configuration for anything an experiment depends on.

## 2026-11-22 — Inferred causation from NetworkManager being active
What I believed / predicted: `systemctl is-active NetworkManager` returning `active` proved NetworkManager had removed my temporary address.
What actually happened: The output only established that NetworkManager was running, not what removed the address.
Why: I read more into the command output than it actually showed.
What I'll do differently: State what each output establishes, and keep competing explanations open until one is tested.

## 2026-11-22 — Confused `ipv4.method auto` with `connection.autoconnect`
What I believed / predicted: Setting `ipv4.method auto` would make my static Ethernet address return automatically after reboot.
What actually happened: `auto` normally means DHCP; a static address needs `ipv4.method manual`, while `connection.autoconnect yes` controls whether the profile activates automatically.
Why: I assumed two settings named "auto" controlled the same behaviour.
What I'll do differently: Check what each configuration setting does before relying on its name.

## 2026-11-22 — Blamed routing for a failed ping
What I believed / predicted: A failed ping meant the routing choice was wrong.
What actually happened: The route correctly selected `eth0`, but `ip neigh` showed `10.10.10.2 FAILED`, meaning ARP could not resolve Pi B's MAC address because Pi B was not yet configured.
Why: I treated routing, neighbour resolution, and delivery as a single step.
What I'll do differently: Diagnose failures layer by layer: address → route → neighbour → delivery.

## 2026-11-22 — Mixed up propagation delay and transmission delay
What I believed / predicted: A larger packet would increase propagation delay, and a longer cable would increase transmission delay.
What actually happened: Propagation delay depends on cable distance and signal speed; transmission delay depends on packet size and link bit rate.
Why: I conflated the signal travelling through the medium with the time taken to put bits onto the link.
What I'll do differently: Define each latency component and its variables before reasoning about what changes it.

## 2026-11-22 — Assumed full duplex could hide round-trip dependency
What I believed / predicted: Full duplex and multithreading could make round-trip latency resemble one-way latency.
What actually happened: Pi A's reply cannot exist until it has received and processed the order, so the causal dependency remains.
Why: I confused simultaneous bidirectional transmission with removing the request-response sequence.
What I'll do differently: Map the causal chain of a transaction before proposing concurrency as a latency optimisation.

## 2026-11-22 — Treated configuration evidence as packet evidence
What I believed / predicted: Correct IP and `ip route get` outputs proved application traffic would use Ethernet.
What actually happened: These show configuration and the kernel's route choice for one destination, not what the application actually sends.
Why: An application could connect to a different address, bind to another interface, or run under different routing conditions.
What I'll do differently: Confirm the application targets `10.10.10.1` and capture its packets on `eth0` with `tcpdump` before benchmarking.