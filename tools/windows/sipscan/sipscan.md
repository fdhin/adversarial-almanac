---
name: "sipscan"
categories:
  - blackarch-windows
version: "0.1"
homepage: "http://www.hackingvoip.com/sec_tools.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-13"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/sipscan"
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

# sipscan

> Windows GUI SIP scanner from the Hacking Exposed VoIP authors for finding SIP devices and enumerating valid SIP usernames.

## What it's for

SIPScan is a Windows-based SIP enumeration tool written by David Endler and Mark Collier and distributed through their hackingvoip.com companion site for the book *Hacking Exposed: VoIP*; it appears in the book's enumeration chapter alongside sipsak and smap. It probes SIP-enabled targets using INVITE, REGISTER and OPTIONS signaling messages to discover SIP-capable devices on a network and to enumerate valid SIP usernames/extensions against a PBX or proxy — the reconnaissance step that precedes password and registration attacks. It is a point-and-click GUI application distributed as an MSI installer, with no command-line interface; for scripted SIP discovery on modern systems, tools such as SVMap from the SIPVicious suite fill the same role. The original site is long gone, and the MSI redistributed in security-tool archives is the practical reference copy.

GitHub: third-party mirror of hackingvoip.com SIPScan (Seabreg/sipscan).

*No corpus evidence — derived from upstream documentation.*
