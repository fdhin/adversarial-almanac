---
name: "snscan"
categories:
  - blackarch-windows
version: "1.05"
homepage: "http://www.mcafee.com/uk/downloads/free-tools/snscan.aspx"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-13"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "shifty0g/old-win-pentest-tools"
github-stars: 1
github-pushed: "2022-11-09"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snscan

> SNScan — Foundstone's Windows utility for quickly identifying SNMP-enabled devices on a network.

## What it's for

SNScan v1.05 is one of Foundstone's free security utilities, redistributed from McAfee's free-tools pages after the acquisition. It is a Windows-based SNMP detection utility: point it at a host or range and it quickly and accurately identifies which devices run SNMP responders, indicating systems potentially exposed to SNMP-related security threats — the classic exposure of that era being default community strings such as `public` on routers, printers and Windows hosts. The name is short for SNMP scan (not NetBIOS). It is a GUI tool with no documented command-line interface; the modern scripted equivalents are `onesixtyone`, `snmpwalk` and nmap's SNMP NSE scripts. The original download endpoint is dead; the Foundstone zip redistributed with security-tool archives is the reference copy, useful today mainly on legacy-assessment engagements and as a historical artifact.

GitHub: third-party mirror of the Foundstone distribution, hosted in the shifty0g/old-win-pentest-tools collection.

*No corpus evidence — derived from upstream documentation.*
