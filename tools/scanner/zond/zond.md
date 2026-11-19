---
name: "zond"
categories:
  - blackarch-scanner
version: "0.19.0.r10.g1e9a2a4"
homepage: "https://github.com/zond-rs/zond"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: go
license: AGPL-3.0-or-later, CC-BY-SA-4.0
meta-checked: "2026-11-09"
github-repo: "zond-rs/zond"
github-stars: 2
github-pushed: "2026-10-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 231
gh-activity-checked: "2026-11-09"
---

# zond

> Rust network scanner (CLI for zond-engine) that finds hosts, open ports and services and shows the CVEs that still apply to each.

## What it's for

Zond covers the discovery-to-CVE pipeline in one binary: find live hosts on a network, scan their ports, identify the services behind them, and match those services against distribution security data so each result arrives with the CVEs that still apply. It is the official command-line interface to the zond-engine Rust library, runs on Linux, macOS and Windows, and is built for repeatable audits — every scan is journaled, can be resumed, reprinted, exported (JSON, CSV, HTML, nmap XML) and diffed against an earlier scan to show exactly what changed on the network. Raw-socket scan types normally need privileges, but zond degrades gracefully to ordinary connections and tells you what it could not do. Think nmap-with-CVE-context for ongoing network monitoring rather than one-off red-team sweeps.

## Typical use cases

### Discover which hosts are up

```
zond discover lan
```

Outcome: sweeps the local network (the `lan` keyword auto-targets it; addresses, ranges like `10.0.0.1-50`, CIDRs and hostnames also work) and prints the live hosts found — the starting point before any port scan.

### Scan one host's ports and services

```
zond scan 198.51.100.10
```

Outcome: enumerates the host's open ports, identifies the service behind each and lists the CVEs that still apply to those service versions.

### Quick pass over a whole subnet

```
zond scan 198.51.100.0/24 -F
```

Outcome: `-F` runs the fast profile across every address in the CIDR — fewer probes per host, traded for coverage when surveying a large network.

### Deep scan every port with OS detection

```
zond scan 198.51.100.10 -p- -A
```

Outcome: all 65535 ports (`-p-`) with the aggressive profile (`-A`) — deeper checks including OS fingerprinting; the slowest, most complete single-host pass.

### Feed scans into scripts

```
zond scan lan --pipe
```

Outcome: emits one line per host instead of the interactive view — designed for piping into other tools, logging, or cron-driven network inventory.

### Review saved scans

```
zond journal
```

Outcome: lists the scans stored on the machine; paired with `zond read` (reprints or exports an old scan) and `zond diff` (shows what changed between two scans), this is the audit-trail side of the tool — no rescan needed to compare today with last month.

*No corpus evidence — derived from upstream documentation.*
