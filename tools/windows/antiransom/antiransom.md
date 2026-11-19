---
name: "antiransom"
categories:
  - blackarch-windows
version: "5"
homepage: "http://www.security-projects.com/?Anti_Ransom___Download"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-26"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "YJesus/AntiRansom"
github-stars: 180
github-pushed: "2021-11-07"
github-archived: false
github-issues: 5
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# antiransom

> Windows anti-ransomware tripwire by YJesus (security-projects.com): a honeypot folder of decoy documents whose modification gets the offending process stopped and memory-dumped.

## What it's for

AntiRansom takes the honeypot approach to ransomware: it creates a random decoy folder filled with useless random Excel and PDF documents and watches it for changes. When something starts modifying the bait, AntiRansom identifies the process responsible, stops it and dumps its memory — the rationale being that the ransomware's key is likely still in that process's memory at that moment. Version 5 tracks the honey folder with Sysinternals handle for accuracy and checks the touching process's Authenticode signature: a signed process raises an alert, an unsigned one is stopped and alerted; an "enforced" parameter enables blocking without user interaction, logging to the Windows Event Log for large deployments. It is a GUI/installer Windows application — the release ships setup.exe, monit.exe, splash.exe and generator.exe — with no documented command-line interface, so no CLI examples; BlackArch carries the binaries under /usr/share/windows/antiransom/. The same author's [[brute12]] audits PKCS#12 container passwords.

*No corpus evidence — derived from upstream documentation.*
