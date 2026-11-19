---
name: "network-app-stress-tester"
categories:
  - blackarch-dos
version: "19.df75391"
homepage: "https://github.com/PherricOxide/Network-App-Stress-Tester"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-18"
build-system: other
license: GPL3
meta-checked: "2026-11-09"
github-repo: "PherricOxide/Network-App-Stress-Tester"
github-stars: 6
github-pushed: "2012-10-12"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# network-app-stress-tester

> Tiny raw-socket stress tool that floods a target while fuzzing packet fields such as ports, payload size, and timing.

## What it's for

A single-file C research utility (built from `src/fast.c`, installed as `network-app-stress-tester`) that sends large volumes of raw packets to a chosen destination and can randomize or step the source IP, source port, payload size, and inter-packet interval while doing so. It is meant for observing how network stacks, firewalls, and applications behave under floods and unusual traffic distributions — the companion `src/novatester.sh` shows the intended workflow of combining it with nmap scan variants. It has no dependencies and needs root for its raw sockets. It is a 2012-era experiment: no protocol intelligence, no reporting, and running it with no arguments prints the current usage/options list.

## Typical use cases

### Print the live usage list

See the exact options the binary supports on your build.

```
network-app-stress-tester
```

Outcome: prints current usage instructions and options to stdout — the authoritative list, since the README notes options "may not apply to all fuzzing types".

### Fixed-rate flood at one target

Send a bounded number of packets to a specific address and port.

```
network-app-stress-tester -dstip 198.51.100.10 -dstport 5060 -packetcount 1000 -interval 10
```

Outcome: emits 1000 packets toward 198.51.100.10:5060 at the given inter-packet interval and exits; watch the receiving side's counters or capture to confirm the rate and volume.

### Fuzz source ports

Scatter the flood across random or sequential source ports to stress connection-tracking tables.

```
network-app-stress-tester -dstip 198.51.100.10 -dstport 5060 -fuzzRandomSrcPort -packetcount 2000
```

Outcome: 2000 packets reach the target with randomized source ports — a state-table exhaustion pattern; swap `-fuzzSequentialSrcPort` to sweep them in order.

### Fuzz payload size

Vary how much data each packet carries, from undersized to oversized.

```
network-app-stress-tester -dstip 198.51.100.10 -dstport 5060 -fuzzPayloadSize -payloadmin 1 -payloadmax 1200 -packetcount 200
```

Outcome: 200 packets are sent with payload sizes varying between 1 and 1200 (the combo the project's own novatester.sh uses), exercising fragmentation and buffer handling on the path.

### Randomize source addresses

Simulate a distributed flood from one host.

```
network-app-stress-tester -dstip 198.51.100.10 -dstport 5060 -fuzzRandomSrcIp -packetcount 2000
```

Outcome: 2000 packets arrive at the target carrying random spoofed source IPs — use it in a lab to test ingress filtering, SYN-cookie, or anti-spoofing alerting.

*No corpus evidence — derived from upstream documentation.*
