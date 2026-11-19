---
name: "chiron"
categories:
  - blackarch-scanner
version: "48.524abe1"
homepage: "http://www.secfu.net/tools-scripts/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "aatlasis/Chiron"
github-stars: 208
github-pushed: "2018-11-16"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chiron

> IPv6 security assessment framework: host/port scanning, neighbor-discovery recon, extension-header attack traffic and an IPv4-to-IPv6 proxy, built on Scapy.

## What it's for

Chiron (Antonios Atlasis) is a Python framework for IPv6 security testing, packaged as separate commands: chiron-scanner (host and port scanning), chiron-local-link (neighbor-discovery and MLD recon), chiron-attacks (IPv6 attack traffic), chiron-proxy (an IPv4-to-IPv6 relay), plus the chiron-nd and chiron-combinations helper commands. Its distinguishing feature is a shared library for building completely arbitrary IPv6 extension-header chains, fragmented or not — useful for testing IDS/IPS and firewall evasion and for fuzzing how IPv6-capable devices parse extension headers. It uses its own Scapy-based sniffer rather than the OS stack, needs root, and is a python2-era tool. It does not do IPv4 scanning or web-layer testing.

No corpus evidence — derived from upstream source.

## Typical use cases

### Scan IPv6 hosts on a link

Find live hosts and open ports in an IPv6 range.

```
chiron-scanner eth0 -sM -of results.txt
```

Outcome: a smart scan of the IPv6 address space on eth0 (host discovery plus common TCP/UDP ports); discovered hosts and open ports are printed and written to results.txt. Target specific addresses with `-d 2001:db8::1,2001:db8::2` or a list file with `-iL targets.txt`, and tune the scanned prefix with `-pr`.

### Map the local link's neighbor-discovery data

Recon IPv6/MAC bindings and multicast listeners on the segment.

```
chiron-local-link eth0 -mrec -of local-link.txt
```

Outcome: neighbor-solicitation results (IPv6-to-MAC mappings) and MLD recon output are collected on eth0 and saved to local-link.txt.

### Relay IPv4 tooling onto an IPv6 network

Bridge IPv4-only tools to IPv6-only targets.

```
chiron-proxy eth0 eth1 198.51.100.10 198.51.100.20
```

Outcome: the proxy listens on the last IPv4 address given, translates matching traffic onto IPv6 via the first interface, and forwards replies back to the IPv4 sender, so IPv4-only tools can reach IPv6 targets. Positional arguments are: IPv6 interface, IPv4 interface, IPv4 sender address, IPv4 listening address.

### Send crafted fragmented extension-header chains

Test how a target handles arbitrary EH chains and fragmentation.

```
chiron-attacks eth0 -d 2001:db8::1 -nf 8
```

Outcome: attack traffic built from an 8-fragment extension-header chain is sent to the target; `-lfE`/`-luE` place chosen extension headers in the fragmentable/unfragmentable parts of each packet — the mechanism behind Chiron's IDS/firewall-evasion and EH-fuzzing use cases.
