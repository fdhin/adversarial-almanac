---
name: "tcpcontrol-fuzzer"
categories:
  - blackarch-fuzzer
version: "0.1"
homepage: "https://www.ee.oulu.fi/research/ouspg/tcpcontrol-fuzzer"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/tcpcontrol-fuzzer"
github-stars: 0
github-pushed: "2019-09-16"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tcpcontrol-fuzzer

> Perl fuzzer that walks all 64 combinations of the six classic TCP control bits against a chosen host and port.

## What it's for

tcpcontrol-fuzzer (written by "Shadow", 2010, originally to reproduce a JunOS crash) sends 64 crafted TCP packets to a host:port — one for every on/off combination of SYN, ACK, FIN, RST, PSH and URG — each carrying a short payload. It is a quick way to find IP stacks (router OSes, userland stacks, embedded systems) that mishandle unexpected flag combinations which healthy stacks reject. It cannot test the ECE/CWR bits because Net::RawIP does not support them, it needs raw-socket privileges, and it reports nothing beyond per-packet progress output — observe the target side (logs, packet capture, crash) for effects.

## Typical use cases

### Sweep all control-bit combinations at a service

```
sudo tcpcontrol-fuzzer 198.51.100.10 80
```

Outcome: prints and sends 64 packets ("Sending packet N to 198.51.100.10:80 Bits: ..."), enumerating every combination of SYN/ACK/FIN/RST/PSH/URG at that port; watch the target's stack behavior or a parallel capture for resets, hangs or crashes.

### Sweep with a chosen source address

```
sudo tcpcontrol-fuzzer 198.51.100.10 80 198.51.100.20
```

Outcome: the same 64-packet sweep emitted with 198.51.100.20 as the packet source IP, useful to match capture filters or firewall expectations on the target side.

*No corpus evidence — derived from upstream documentation.*
