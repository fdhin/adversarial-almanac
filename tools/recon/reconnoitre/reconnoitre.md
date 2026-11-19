---
name: "reconnoitre"
categories:
  - blackarch-recon
version: "1.r338.gf62afba"
homepage: "https://github.com/codingo/Reconnoitre"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "codingo/Reconnoitre"
github-stars: 2200
github-pushed: "2022-12-11"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# reconnoitre

> Multithreaded host-discovery and service-enumeration helper that builds a per-host engagement directory tree and writes next-step command recommendations.

## What it's for

Reconnoitre automates the first hours of an internal/lab assessment: ping sweeps, hostname and DNS-server discovery, SNMP checks, and Nmap-based service scanning — while creating a structured output directory per host (scans, exploit, loot, proof) and, crucially, writing a findings file that recommends the exact follow-up commands (nikto, dirb, gobuster, enum4linux, nmap script invocations) for every service it finds. It needs `nmap` (and `nbtscan` for host scanning) installed. It is a wrapper and organizer, not a scanner itself — the recommendations name tools you must have available. Development has slowed; its author points users building repeatable pipelines at Interlace instead.

## Typical use cases

### Enumerate a single host and get follow-up recommendations

```
reconnoitre -t 198.51.100.10 -o ./engagement/ --services
```

Outcome: creates `./engagement/198.51.100.10/` with scans/exploit/loot subdirectories, runs a quick then detailed TCP/UDP Nmap scan, and writes a findings file under `scans/` listing each discovered service plus copy-pasteable enumeration commands targeting it.

### Discover live hosts and hostnames across a range

```
reconnoitre -t 198.51.100.1-252 -o ./engagement/ --pingsweep --hostnames
```

Outcome: writes `target.txt` containing the live hosts found by the ping sweep and a `hostnames.txt` with resolved hostnames — the input set for the next, service-level pass.

### Quick first-round service scan of a whole range

```
reconnoitre -t 198.51.100.1-252 -o ./engagement/ --pingsweep --services --quick
```

Outcome: for each live host, performs only the fast Nmap pass and writes first-round recommendations before moving on; drop `--quick` to also run the detailed TCP/UDP scans (much longer runtime).

*No corpus evidence — derived from upstream documentation.*
