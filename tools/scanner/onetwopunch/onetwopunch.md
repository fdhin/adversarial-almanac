---
name: "onetwopunch"
categories:
  - blackarch-scanner
version: "v1.0.0.r2.gd4ab4e8"
homepage: "https://github.com/superkojiman/onetwopunch"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-06"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "superkojiman/onetwopunch"
github-stars: 622
github-pushed: "2018-06-08"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# onetwopunch

> Wrapper that lets unicornscan blast all 65,535 ports quickly, then hands only the open ones to nmap for thorough service detection.

## What it's for

onetwopunch pairs two scanners to dodge nmap's weakness on full-range scans: unicornscan's asynchronous userland TCP/IP stack enumerates all ports on each target fast, and the open ports it finds are fed to nmap (`-sV`-style options of your choosing) so the slow, thorough scan only covers what is actually open. Per-IP logs are kept in organized directories — unicornscan results plus nmap XML and grepable output — so nothing needs re-running. It is a root-requiring shell script that needs both `nmap` and `unicornscan` on PATH, and the interface it scans from matters (default `eth0`). Development stopped in 2018; the pattern itself is still the standard answer to "nmap all ports takes forever".

## Typical use cases

### Fast full-range TCP port scan with nmap follow-up

```
sudo onetwopunch -t targets.txt
```

Outcome: for each IP in the file, unicornscan sweeps all TCP ports, then nmap service-scans only the open ones; per-IP unicornscan lists and nmap XML/grepable output land under the run's log directory for later review.

### Full-range UDP pass

```
sudo onetwopunch -t targets.txt -p udp
```

Outcome: unicornscan `-mU` sweeps all UDP ports and nmap then inspects only the responsive ones — the same two-stage trick for UDP, where blind nmap sweeps are even slower. `-p all` runs both protocols.

### Scan through a tunnel interface with custom nmap options

```
sudo onetwopunch -t targets.txt -i tun0 -n '-A -sV'
```

Outcome: both stages run through the given interface (e.g. a VPN tunnel) and nmap receives your extra options verbatim, so version/OS/script results come back exactly as a hand-written `nmap -A -sV` would produce — scoped to the ports unicornscan found open.

*No corpus evidence — derived from upstream source (usage/getopts block and scan logic in onetwopunch.sh of superkojiman/onetwopunch; install name from the BlackArch PKGBUILD).*
