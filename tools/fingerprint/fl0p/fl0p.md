---
name: "fl0p"
categories:
  - blackarch-fingerprint
version: "0.1"
homepage: "http://lcamtuf.coredump.cx/"
packaged-by: [blackarch]
blackarch-builddate: "2024-01-31"
build-system: other
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fl0p

> Passive layer-7 flow fingerprinter — classifies TCP/UDP/ICMP sessions by packet sizes, timing and ordering alone, without inspecting payloads or port numbers.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Telling what kind of activity generated a network flow when you cannot or should not look at the content. fl0p (by Michal Zalewski, derived from the p0f codebase) fingerprints layer-7 behavior purely from flow characteristics — size ratios, delays, packet ordering. Because payload and ports are ignored, the technique survives encryption and obfuscation: an SSL-tunneled backdoor on port 443 is distinguishable from browser traffic, and successful versus failed SSH authentication attempts can be told apart remotely. It also separates humans from robots — manual SMTP or SSH interaction stands out from scripted clients, so single users can be picked out of bot noise. It can watch a live interface, replay saved tcpdump captures for forensics, or run as a daemon logger. A 2006 beta: it does no stateful stream inspection and the author notes it is trivially bypassed (invalid-checksum RSTs, fragmentation, retransmission); everything it can recognize is defined in the `fl0p.fp` signature file (`/etc/fl0p/fl0p.fp` when packaged).

## Typical use cases

### Passively fingerprint traffic on a live interface

Watch a link and classify sessions as they close.

```
sudo fl0p -i eth0
```

Outcome: prints a fingerprint verdict per finished or idle session (matched signature with confidence) as traffic flows; sessions with no known signature stay silent unless `-U` is given.

### Analyze a saved capture

Re-examine historical traffic without touching the network.

```
fl0p -s capture.pcap
```

Outcome: reads packets from a tcpdump/pcap file instead of a live device and prints the same per-session fingerprint verdicts — the intended mode for forensic review of old captures.

### Run as a background logger

Leave a persistent flow-classification sensor on a host.

```
sudo fl0p -i eth0 -d -o fl0p.log -u nobody
```

Outcome: forks into the background (`-d` requires `-o`), appends timestamped verdicts to fl0p.log, and chroots/setuids to nobody — a set-and-forget sensor for long-running monitoring.

### Collect new signatures

Extend recognition to traffic the stock database misses.

```
fl0p -i eth0 -K
```

Outcome: suppresses known-signature output (`-K` implies `-U`) and instead prints a fingerprint block for every unidentified session, which you can study and add to fl0p.fp to teach fl0p new flows.
