---
name: "iaxflood"
categories:
  - blackarch-dos
version: "0.2"
homepage: "https://www.hackingexposedvoip.com/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-12-18"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/iaxflood"
github-stars: 0
github-pushed: "2025-12-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# iaxflood

> Single-purpose IAX2 protocol flooder from the Hacking Exposed VoIP toolset, in a modernized build.

## What it's for

iaxflood sends a stream of IAX (Inter-Asterisk eXchange, version 2) call-signaling packets at a VoIP server to test how it copes with high-volume signaling traffic. It comes from the Hacking Exposed VoIP companion tools (Collier/O'Brien, SecureLogix); this build is a refactor of the original that fixes compilation against current libraries. The interface is exactly three arguments — source host, destination host, packet count — with both UDP ports pinned to 4569, the well-known IAX port, and no other options. Use it against a PBX you own to measure at what packet rate call setup starts to lag or fail.

### Flood a PBX's IAX2 port

The only thing the tool does, in one line.

```
sudo iaxflood 198.51.100.20 198.51.100.10 100000
```

Outcome: prints `Will flood port 4569 from port 4569 100000 times` and sends that many pre-built IAX2 packets claiming source 198.51.100.20 at the PBX at 198.51.100.10. On the PBX, watch the IAX channel/registration diagnostics (for example `iax2 show channels` on Asterisk) swell and measure how real call-setup latency degrades during the run. Requires root to inject the forged packets.

*No corpus evidence — derived from upstream documentation.*
