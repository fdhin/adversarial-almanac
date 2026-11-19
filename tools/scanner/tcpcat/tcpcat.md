---
name: "tcpcat"
categories:
  - blackarch-scanner
version: "v1.5.0.r6.g6f93eb0"
homepage: "https://github.com/NycolazSec/tcpcat"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: go
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "NycolazSec/tcpcat"
github-stars: 11
github-pushed: "2026-11-04"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 203
gh-activity-checked: "2026-11-09"
---

# tcpcat

> High-performance network reconnaissance and vulnerability intelligence scanner with an eBPF/AF_XDP engine.

## What it's for

tcpcat is a Go-based network scanner for authorized assessments: host discovery, port scanning, service and version fingerprinting, and CVE correlation against the OSV vulnerability API, with JSON and SARIF 2.1.0 reporting. Its eBPF/AF_XDP engine moves the send/receive path into the kernel to sustain very high packet rates, while its evasion options (jitter, fragmentation, decoys, TTL manipulation) are framed by the project as a way to validate how monitoring controls record scan traffic — not a guaranteed IDS bypass. Built-in profiles such as `safe-production` and `ot`, plus `--scope-file` target lists, keep scans inside explicitly authorized boundaries. It needs root for raw sockets, and its nmap-style flags (`-Pn`, `-sV`, `-sS`, `-sT`, `-p`, `-T`) make migration easy.

## Typical use cases

### Scan a host with service and version detection

```bash
sudo tcpcat -sV -p 22,80,443 target.example.com
```

Outcome: prints live-host discovery, per-port OPEN/CLOSED results with reply reasons, detected service/version banners with missing HTTP security-header notes, and correlated CVEs with CVSS scores; add `-j report.json` or `--sarif report.sarif` for machine-readable output.

### Rapid topology mapping at high rate

```bash
sudo tcpcat -sS --top-ports 1000 --rate 100000 198.51.100.0/24
```

Outcome: SYN scan of the 1000 most common ports at up to 100,000 packets/second across the subnet, summarizing which hosts expose which top ports.

### Ultra-fast enumeration with the eBPF engine

```bash
sudo tcpcat --ebpf -p 1-1000 198.51.100.0/16 -w 128 -T 5
```

Outcome: kernel-space eBPF/AF_XDP scanning with 128 concurrent workers at timing template 5 — the fastest path over large authorized ranges.

### Gently scan OT/ICS networks

```bash
sudo tcpcat --profile ot --scope-file scope.txt 198.51.100.0/24
```

Outcome: full TCP connect scanning only (never raw SYN, UDP or eBPF), serial connections at 5 pps, over a curated set of industrial control ports (Modbus 502, S7comm 102, EtherNet/IP 44818, BACnet 47808, ...) that are named passively; `--ot-probe` opts into one well-formed read-only Modbus or EtherNet-IP identification query per supported port for vendor/version and CVE correlation.

### Diff scans against a previous baseline

```bash
sudo tcpcat --profile safe-production --scope-file scope.txt -Pn -p 443 -sV -j state.json --baseline state.json 198.51.100.0/24
```

Outcome: each run compares against the previous JSON report (newly open ports, service/version changes, newly correlated CVEs) and then becomes the next baseline, which suits scheduled monitoring — pair with `--notify-webhook` to alert only on changes.

*No corpus evidence — derived from upstream documentation (NycolazSec/tcpcat README quick start and Common Scenarios sections; BlackArch PKGBUILD installs the Go binary as `tcpcat`).*
