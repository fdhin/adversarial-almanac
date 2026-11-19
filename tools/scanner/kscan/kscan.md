---
name: "kscan"
categories:
  - blackarch-scanner
version: "v1.85.r55.gcf76af2"
homepage: "https://github.com/lcvvvv/kscan"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-07"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "lcvvvv/kscan"
github-stars: 4296
github-pushed: "2023-08-22"
github-archived: false
github-issues: 52
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kscan

> All-in-one Go asset scanner: port scanning, protocol detection, application fingerprinting and optional brute-force.

## What it's for

Kscan (lcvvvv) is a lightweight, purely Go "all-direction" asset scanner aimed at
internal/external attack-surface mapping. Its defining choice is that targets are not
pre-classified: IPs, CIDRs, ranges, URLs and file lists all go into `-t`, and every
open port gets real protocol identification (1200+ protocols) rather than
port-to-service guessing, followed by application fingerprinting (20000+ fingerprints),
title grabbing and CDN detection on web ports. Output is per-port asset information,
to the terminal or a file (`-o`, `-oJ` for JSON). A `--spy` mode discovers live
internal subnets, `-f` pulls targets from FOFA queries, and an integrated brute-forcer
covers ssh, rdp, ftp, smb, mysql, mssql, oracle, postgresql, mongodb and redis. The
documentation and console output are in Chinese. Use it when you want one pass that
turns a raw IP range into labeled services; it is not a vulnerability scanner and
ships no exploit checks.

## Typical use cases

### Port-scan and fingerprint a target

```
kscan -t 198.51.100.10
```

Outcome: Scans the default top-400 ports (narrow with `-p 80,443,8080-8090`, widen with `--top 1000`), identifies the protocol on every open port and runs application fingerprinting on web services, printing per-port results with service, title and fingerprints; add `-oJ out.json` to keep them.

### Map an entire range or a URL list from a file

```
kscan -t file:/tmp/targets.txt --proxy socks5://198.51.100.10:1080
```

Outcome: Accepts the same mixed formats from a file (IPs, ranges, URLs — URLs keep their path for detection), optionally routing all probes through the given proxy, so pivoted sweeps through a foothold look like normal client traffic.

### Discover live internal subnets

```
kscan --spy all --scan
```

Outcome: Probes all private ranges (`192.168/172.16-31/10`) for surviving gateways and hosts; with `--scan` the discovered segments are immediately port-scanned and fingerprinted, giving a one-shot map of an unknown internal network from a single vantage point.

### Brute-force discovered services

```
kscan -t 198.51.100.10 -p 22,3306 --hydra --hydra-user root --hydra-pass file:passwords.txt --hydra-mod ssh,mysql
```

Outcome: After scanning, launches built-in dictionary attacks for the selected services and protocols with your chosen usernames/passwords (single values, comma lists or files), reporting any working credentials alongside the port results.

*No corpus evidence — derived from upstream documentation.*
