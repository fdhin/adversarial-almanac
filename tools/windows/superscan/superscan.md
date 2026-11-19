---
name: "superscan"
categories:
  - blackarch-windows
version: "4.1"
homepage: "http://www.foundstone.com/us/resources/proddesc/superscan.htm"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-13"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lucascavalare/SuperScan4"
github-stars: 0
github-pushed: "2017-02-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# superscan

> SuperScan 4 — Foundstone's fast Windows GUI TCP/UDP port scanner, pinger and resolver.

## What it's for

SuperScan (Foundstone; version 4, 2003 onward, packaged here as 4.1) was the port-scanning workbench of its Windows era: a "powerful TCP port scanner, pinger, resolver" in the vendor's own description. Foundstone's version 4 feature list: superior scanning speed, support for unlimited IP ranges, host detection using multiple ICMP methods, TCP SYN scanning, UDP scanning by two methods, IP-address import supporting ranges and CIDR formats, source-port scanning, fast hostname resolving, extensive banner grabbing, a massive built-in port-description database, IP and port scan-order randomization, simple HTML report generation, and a side menu of utility tools such as ping and traceroute. It is a GUI application with no command-line interface. Two era notes: Windows XP Service Pack 2 removed raw sockets, degrading SuperScan and similar tools on that platform, and the Foundstone free-tools catalog is long gone from McAfee's live site — the redistributed Foundstone zip is the reference copy. Modern scripted equivalents are nmap and masscan; SuperScan remains relevant when working with legacy Windows assessment images.

GitHub: third-party mirror of the Foundstone distribution (lucascavalare/SuperScan4).

*No corpus evidence — derived from upstream documentation.*
