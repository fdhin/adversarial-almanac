---
name: "attk"
categories:
  - blackarch-scanner
version: "2.0.1023"
homepage: "https://spnsupport.trendmicro.com/"
packaged-by: [blackarch]
blackarch-builddate: "2020-09-04"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# attk

> Trend Micro Anti-Threat Toolkit (ATTK) — a standalone malware scan-and-clean and diagnostic-collection toolkit that runs without installing a full Trend Micro product, in online or offline variants.

*No corpus evidence — derived from upstream documentation. Description-only: the console builds ship no documented command-line interface, so no example commands are given.*

## What it's for

Cleaning an endpoint that regular antivirus cannot handle and packaging evidence for analysis. ATTK scans for common malware families (ransomware variants, Trojans and fileless threats), deletes or repairs what it finds, and collects suspicious files, system information and product logs into a timestamped ZIP under a `TrendMicro AntiThreat Toolkit/Output/` folder, together with a temporary ID for handing the bundle to Trend Micro support. It does not rely on the normal antivirus pattern file, so it is aimed at stubborn or undetected infections rather than day-to-day protection. The canonical interface is interactive — on Windows a GUI wizard (Scan Now → review findings → Fix Now); the BlackArch package installs Trend Micro's Linux console builds as `attk-cli` and `attk-tui` and keeps the Windows build under `/usr/share/attk/`. There is no published flag reference for the console builds, so they are documented here as interactive scanners rather than with invented options.
