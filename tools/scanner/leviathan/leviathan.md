---
name: "leviathan"
categories:
  - blackarch-scanner
version: "35.a1a1d8c"
homepage: "https://github.com/leviathan-framework/leviathan"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "leviathan-framework/leviathan"
github-stars: 1044
github-pushed: "2018-10-03"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# leviathan

> Mass-audit framework chaining service discovery, brute force, SQLi scanning and custom exploits across wide IP ranges.

## What it's for

Leviathan (by Utku Sen and Ozge Barbaros) wraps masscan, ncrack and DSSS behind one menu-driven framework for auditing large numbers of systems: discover FTP, SSH, Telnet, RDP and MySQL services across a country code or IP range (via Shodan, Censys or the integrated masscan), brute-force the found services with bundled wordlists, scan websites for SQL injection using your own dork or a country extension, and mass-run bundled or custom exploits against discovered victims. It is built for wide surveys, not single-target work. The project is archived (last push 2018) and requires Python 2.7 — treat it as a legacy toolkit.

## Typical use cases

### Drive a wide-range audit from the menus

Launch the framework and work through its guided menus.

```
leviathan
```

Outcome: opens the interactive main menu (discovery, brute force, SQL injection, exploit execution); each entry walks you through choosing targets — IP range, country, or Shodan/Censys query — and options, then runs the underlying masscan/ncrack/DSSS stage and reports per-module results. The BlackArch wrapper runs the packaged `leviathan.py` with Python 2.
