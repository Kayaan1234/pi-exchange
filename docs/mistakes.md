## 2026-11-19 — Default compiler warnings were insufficient
What I believed / predicted: GCC would detect common programming mistakes by default.
What actually happened: Unused variables and signed/unsigned comparisons compiled silently until warning flags were enabled.
Why: GCC does not enable all available diagnostics by default.
What I'll do differently: Explicitly enable and test warning flags from the beginning.

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

## 2026-11-22 — Invented a cause for the disappearing Ethernet address
What I believed / predicted: When `ip route get` returned Wi-Fi again, I assumed Linux had searched for Pi B, failed, and fallen back to the default route. I then treated `systemctl is-active NetworkManager` returning `active` as proof that NetworkManager had removed my temporary address.
What actually happened: `ip -4 addr` and `ip -4 route` showed the temporary `eth0` address and its connected route had been removed. The NetworkManager output only established that it was running, not what removed the address.
Why: `ip addr add` only changes the running kernel state; it is not saved configuration and can be lost on reboot or by network-management activity. I also read more into the command output than it actually showed.
What I'll do differently: Inspect the current state before explaining unexpected behaviour, state what each output establishes, keep competing explanations open until one is tested, and use persistent configuration for anything an experiment depends on.

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

## 2026-11-22 — Treated configuration evidence as packet evidence
What I believed / predicted: Correct IP and `ip route get` outputs proved application traffic would use Ethernet.
What actually happened: These show configuration and the kernel's route choice for one destination, not what the application actually sends.
Why: An application could connect to a different address, bind to another interface, or run under different routing conditions.
What I'll do differently: Confirm the application targets `10.10.10.1` and capture its packets on `eth0` with `tcpdump` before benchmarking.

## 2026-11-23 — Assumed the Pis were quiet benchmarking environments
What I believed / predicted: Both Pis were clean, so background load wouldn't affect benchmarks.
What actually happened: `ps aux` showed VS Code server processes and desktop components on both Pis, plus a Copilot runtime on Pi A.
Why: Low reported CPU% (~0.2%) doesn't rule out brief bursts that could cause latency spikes.
What I'll do differently: Check running processes, CPU frequency and temperature before each benchmark, and stop unneeded services first.