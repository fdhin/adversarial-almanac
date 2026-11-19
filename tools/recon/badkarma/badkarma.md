---
name: "badkarma"
categories:
  - blackarch-recon
version: "85.2c46334"
homepage: "https://github.com/r3vn/badKarma"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-23"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "r3vn/badKarma"
github-stars: 437
github-pushed: "2018-12-17"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# badkarma

> GTK-driven network reconnaissance workspace: run host scans and interact with targets from one GUI.

## What it's for

badKarma (r3vn) is a GUI reconnaissance toolkit for network infrastructure assessments. Instead of juggling terminal tabs, you manage hosts, run nmap-based discovery and service scans, take screenshots of web services, open VNC/RDP/SSH/telnet sessions and geolocate or photograph targets from a single GTK window, saving everything in session files you can reopen later. It is interactive by design — there is no batch/CLI scanning surface; the only command-line option is reopening a saved session. Development stopped in 2018 and the GTK 3 dependencies of that era (WebKit, VNC, OSM map widgets) can be the hardest part of running it today.

## Typical use cases

### Start the reconnaissance GUI

```
badkarma
```

Outcome: opens the badKarma GTK application window; from there you create a session, add target ranges, launch host/service discovery and interact with found hosts — all state is kept in the session you save from the menu.

### Reopen a previous session

```
badkarma --session saved_session
```

Outcome: loads the saved session file and restores its hosts, notes and results so an assessment can continue where it left off.

*No corpus evidence — derived from upstream documentation.*
