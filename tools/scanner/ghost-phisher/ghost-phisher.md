---
name: "ghost-phisher"
categories:
  - blackarch-scanner
version: "2.0"
homepage: "https://github.com/Obsidian-Covenant/ghost-phisher"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/ghost-phisher"
github-stars: 2
github-pushed: "2025-12-15"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# ghost-phisher

> GUI wireless and Ethernet attack suite: fake access point, inbuilt DHCP/DNS/HTTP servers and a credential-logging phishing stack in one PyQt window.

## What it's for

Ghost Phisher bundles the moving parts of a phishing engagement into a single desktop
application: it emulates a Wi-Fi access point, runs an RFC 2131 DHCP server and an RFC 1035 DNS
server, hosts the phishing pages and logs submitted credentials to SQLite, and additionally
offers passive/ Ethernet session hijacking, ARP cache poisoning and optional Metasploit payload
bindings. The BlackArch package builds the modernized Python 3 / PyQt6 continuation of the
original project. It is a GUI program — everything is driven from the window it opens, there is
no scriptable CLI and no headless mode. It needs root and a wireless-capable host for the
access-point features.

## Typical use cases

### Launch the suite

Start the application on a host with the wireless stack available.

```
sudo ghost-phisher
```

Outcome: opens the PyQt6 application window (on a source checkout the equivalent is
`sudo python ghost.py`). Each capability — fake access point, DNS, DHCP, HTTP page hosting with
credential logging, ARP poisoning, session hijacking — is configured and started from its own
pane, and captured credentials accumulate in the built-in SQLite logger as the phishing pages
see submissions.

*No corpus evidence — derived from upstream documentation (Obsidian-Covenant/ghost-phisher README
and the BlackArch launcher wrapper around ghost.py).*
