---
name: "umit"
categories:
  - blackarch-networking
version: "1.0"
homepage: "http://www.umitproject.org/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "umitproject/network-scanner"
github-stars: 14
github-pushed: "2020-07-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# umit

> Nmap's GTK frontend from the Umit project (started by Adriano Monteiro Marques in Google Summer of Code 2005) — scan profiles, a command-creation wizard, saved-and-comparable results and a scan scheduler.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Driving nmap from a desktop application instead of a shell: build scans through a command-creator wizard, save them as reusable profiles for faster repeat scanning, browse results in a structured view, and diff stored results against each other to spot what changed on a network between scans. It wraps the nmap binary (which must be installed) — it adds no scanning engine of its own — and its UI is PyGTK, so it needs an X session and is at home on the GTK 2 era of desktops. Scanning happens in the GUI; the only scriptable surface is the scheduler control script that starts, stops and inspects the background scan scheduler.

## Typical use cases

### Launch the scanner GUI

```
umit
```

Outcome: the GTK application opens with the profile selector and target entry; picking a profile and entering a target runs nmap behind the window and renders ports/hosts/OS details as structured results that can be saved into the inventory for later comparison. Requires an X display and nmap on PATH.

### Check the scan scheduler state

```
umit_scheduler.py running /tmp/umit-sched
```

Outcome: reports whether the background scheduler for the given config directory is currently running — the same script accepts `start`, `stop` and `cleanup` to manage the daemon that executes scans scheduled from the GUI's scheduler tab.
