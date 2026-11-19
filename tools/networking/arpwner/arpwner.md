---
name: "arpwner"
categories:
  - blackarch-networking
version: "26.f300fdf"
homepage: "https://github.com/ntrippar/ARPwner"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-13"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "ntrippar/ARPwner"
github-stars: 92
github-pushed: "2017-04-16"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# arpwner

> GTK+ GUI for ARP poisoning, passive sniffing, HTTPS stripping and DNS spoofing with per-protocol credential capture.

## What it's for

A Python 2 / PyGTK graphical tool for man-in-the-middle attacks on the local network. From one window you pick an interface, scan an IP range, poison ARP caches, passively sniff traffic, run an SSLStrip-style HTTPS stripper, and spoof DNS responses; while an attack runs it parses cleartext protocols (FTP, HTTP, IMAP, IRC, NNTP, POP3, SMTP, telnet) and lists captured credentials in the GUI. It has no command-line options — the entire workflow is driven from the window that opens on launch. Written for Python 2 with PyGTK (2013-era dependencies), and it needs root for raw packet access.

## Typical use cases

### Launch the attack GUI

Start the man-in-the-middle workbench.

```
sudo arpwner
```

Outcome: opens the GTK+ window (the installed launcher runs `python2 main.py` from the tool's share directory); select a network interface, scan a range with the scan dialog, then enable ARP poisoning, sniffing, HTTPS strip or DNS spoof from the controls — captured protocol credentials accumulate in the session list as traffic flows.

*No corpus evidence — derived from upstream documentation.*
