---
name: "detect-sniffer"
categories:
  - blackarch-defensive
version: "159.a359262"
homepage: "https://github.com/galkan/tools/tree/master/detect_sniffer"
packaged-by: [blackarch]
blackarch-builddate: "2024-09-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "galkan/tools"
github-stars: 99
github-pushed: "2021-05-20"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# detect-sniffer

> Scapy-based detector that finds promiscuous-mode interfaces — the signature of a running network sniffer — by probing hosts with fake-broadcast ARP requests.

## What it's for

detect-sniffer answers one question: is any host on the local segment sniffing traffic? It exploits a side effect of promiscuous mode by sending ARP requests addressed to fake broadcast MAC addresses (`ff:ff:ff:ff:ff:fe`, `ff:ff:00:00:00:00`). A well-behaved network stack ignores those frames; a promiscuous interface answers them, exposing the sniffer (tcpdump, Wireshark, ettercap and friends). The tool probes a single IP or sweeps an entire subnet and prints its verdict to stdout. It is purely a detector — it needs root to send raw packets and neither names the sniffing process nor takes action against it.

## Typical use cases

### Test a single host for promiscuous mode

Check whether one specific machine answers ARP requests it should be ignoring.

```
sudo detect-sniffer -ip 198.51.100.10
```

Outcome: prints `198.51.100.10 :OK` when the host replies to the fake-broadcast ARP — its interface is promiscuous, most likely because a sniffer is running — or `198.51.100.10: NOT` when the host correctly ignores the probe.

### Sweep a subnet for sniffing hosts

Find every promiscuous interface on a segment in one pass.

```
sudo detect-sniffer -net 198.51.100.0/24
```

Outcome: sends fake-broadcast ARP requests across the whole subnet and prints the source address of every replying host; each printed IP is a host whose interface is in promiscuous mode and merits a closer look.

*No corpus evidence — derived from upstream documentation.*
