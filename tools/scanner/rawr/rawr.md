---
name: "rawr"
categories:
  - blackarch-scanner
version: "74.544dd75"
homepage: "https://bitbucket.org/al14s/rawr/wiki/Home"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rawr

> Rapid Assessment of Web Resources — a web-service enumerator that turns nmap/Nessus/Nexpose/Qualys/Metasploit scan data into per-host web enumeration and an attack-surface report.

## What it's for

RAWR (Rapid Assessment of Web Resources, by al14s) automates the grunt work of web enumeration: it takes a target range, a live nmap scan, or XML output from Nessus, Nexpose, Qualys, Metasploit or OpenVAS, finds every host and port running a web service, then visits each one and collects as much data as it can — headers, titles, `robots.txt`, `crossdomain.xml`, SSL properties, screenshots. Results land in a per-run directory as CSV files plus an "Attack Surface Matrix" summary. It is a Python 2 enumerator and reporter, not an exploiter — it does not attack the services it finds.

## Typical use cases

### Enumerate web services across a network segment

Run an nmap-driven sweep of a range and enumerate every web service found.

```
rawr -p 80,443,8080,8088 198.51.100.0/24
```

Outcome: RAWR runs its nmap scan (default timing 4) over the CIDR, then visits each discovered web port, writing `3_Enumeration/rawr_<timestamp>_serverinfo.csv` plus per-host findings CSVs under `3_Enumeration/meta/`, with screenshots of each page unless disabled.

### Build only an attack-surface matrix from existing scan data

Skip the crawling and just correlate scan output you already have.

```
rawr -m "*.nessus"
```

Outcome: parses every `.nessus` file in the current directory (glob input is supported), produces the Attack Surface Matrix, and exits without visiting the targets — a quick triage pass over finished scans.

### Deepen enumeration with extra fetches

Pull additional well-known files and HTTP details for each target.

```
rawr -r -x -o -v 198.51.100.10
```

Outcome: enumerates the single host verbosely (`-v`), additionally requesting `robots.txt` (`-r`) and `crossdomain.xml` (`-x`) and issuing an `OPTIONS` call (`-o`) so allowed HTTP methods are recorded.

### Assess SSL posture during enumeration

Check the TLS configuration of each web target in the same run.

```
rawr --sslv 198.51.100.10
```

Outcome: each target's SSL/TLS security is assessed and folded into the report (upstream marks this option as intrusive).

*No corpus evidence — derived from upstream source (Bitbucket al14s/rawr; flags verified against argparse in `rawr.py`; BlackArch wraps it as `/usr/bin/rawr` running Python 2).*
