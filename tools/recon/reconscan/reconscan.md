---
name: "reconscan"
categories:
  - blackarch-recon
version: "61.afbcfc0"
homepage: "https://github.com/RoliSoft/ReconScan"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "RoliSoft/ReconScan"
github-stars: 412
github-pushed: "2022-01-09"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# reconscan

> Two-script suite: `recon.py` hammers a host with many enumeration tools at once, `vulnscan.py` maps identified software to CVEs and public exploits.

## What it's for

ReconScan is a pentesting workflow helper made of two Python components. `recon.py` drives existing tools (nmap, nikto, dirb, enum4linux, nbtscan, samrdump, onesixtyone, snmpwalk, dig, hydra) to enumerate a host's services exhaustively, runs them mostly in parallel, and aggregates tagged output into a per-host `results/` directory using a `$port_$service_$tool` naming scheme. `vulnscan.py` takes a CPE name, a "name version" string, or an nmap XML report generated with `-sV`, fuzzy-matches software to CPE entries (expanding Debian's CPE alias list so multi-CPE products match correctly), and lists CVEs color-coded by exploit availability with direct ExploitDB/SecurityFocus/Metasploit reference links. It complements, not replaces, commercial scanners; expect it to work best on a Kali-style distro where all the underlying tools are preinstalled.

## Typical use cases

### Exhaustively enumerate every service on a host

```
./recon.py -v 198.51.100.10
```

Outcome: full TCP/UDP Nmap scan followed by per-service tool runs (nikto/dirb on HTTP, enum4linux/samrdump on SMB, snmpwalk on SNMP, zone-transfer attempt on DNS, Nmap script batteries per service), with all output aggregated under `results/198.51.100.10/` as tagged per-tool files; add `-p` to run the tools in parallel.

### Target one service only

```
./recon.py 198.51.100.10 80 http
```

Outcome: skips the all-port discovery phase and runs just the toolchain for that port/service — the fast path when you already know what is listening.

### Preview the tool invocations without running them

```
./recon.py -n 198.51.100.10
```

Outcome: prints every command it would execute (dry run) without invoking anything — useful to audit the aggressive defaults or to harvest the commands for manual use.

### Turn an nmap XML report into CVEs and exploit links

```
./vulnscan.py nmap_scan.xml
```

Outcome: extracts each detected service's CPE from the `-sV` report, looks up its vulnerability history, and prints CVEs color-coded by exploit availability (red = public exploit) with reference links; `-e` filters to only vulnerabilities that have public exploits.

### Look up a software version directly

```
./vulnscan.py 'Apache 2.2.8'
```

Outcome: fuzzy-matches the free text to `cpe:/a:apache:http_server:2.2.8` and lists that entry's CVEs — handy when you have a version banner but no nmap output; run `./vulnscan.py -u` first to (re)download the CVE dumps into the local database.

*No corpus evidence — derived from upstream documentation.*
