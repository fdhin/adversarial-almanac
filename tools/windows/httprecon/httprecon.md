---
name: "httprecon"
categories:
  - blackarch-windows
version: "7.3"
homepage: "http://www.computec.ch/projekte/httprecon/?s=download"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom
meta-checked: "2026-11-09"
github-repo: "Seabreg/httprecon"
github-stars: 0
github-pushed: "2019-03-24"
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

# httprecon

> Windows GUI fingerprinter that identifies web-server software from its HTTP behavior, not its banner.

## What it's for

httprecon is Marc Ruef's web-server fingerprinting research tool (computec.ch, versions up to 7.3, GPL). Instead of trusting the Server banner, it sends a battery of test requests — malformed requests, header-ordering probes, status-code and error-handling checks — and matches the responses against a database of http fingerprints, ranking the most likely web-server products and versions. It is more reliable than banner grabbing against obfuscated servers, fingerprints can be saved and uploaded to improve the shared database, and a PHP web edition existed alongside the Windows binary. The project is defunct (the original site is a challenge-walled corporate page and downloads are gone); BlackArch ships the final 7.3 Windows build, which is a GUI application with no documented command-line interface, so this entry is description-only.

GitHub: third-party mirror of computec.ch httprecon (Seabreg/httprecon, VB6 source tree).

*No corpus evidence — derived from upstream documentation.*
