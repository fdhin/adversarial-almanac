---
name: "tuxcut"
categories:
  - spoof
version: "6.1"
homepage: "https://github.com/a-atalla/tuxcut/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "a-atalla/tuxcut"
github-stars: 250
github-pushed: "2024-11-30"
github-archived: true
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tuxcut

> NetCut for Linux — a root daemon plus wxWidgets GUI that lists LAN hosts, cuts a host's link to the gateway, protects you from other ARP spoofers, and shapes traffic speeds.

## What it's for

The Linux answer to Windows NetCut: discover every live host on the LAN, then ARP-spoof a chosen host against the gateway to sever its internet connection (and restore it again), optionally hide your own IP/MAC from ARP scanners, and rate-limit hosts via wondershaper. Version 6 splits into two parts: `tuxcutd`, a root daemon (installed with a `tuxcutd.service` systemd unit) that does the scapy work behind a REST API bound to 127.0.0.1:8013, and the `tuxcut` wxPython GUI that drives it. Use it defensively too — the protect feature detects and counters other ARP poisoning on the segment. The upstream repository is archived; the packaged 6.1 is the final release.

## Typical use cases

### Start the root daemon

```bash
sudo systemctl start tuxcutd
```

Outcome: the TuxCut server starts as root (equivalently `sudo python3 server/tuxcutd.py` from a source tree) and listens on `127.0.0.1:8013`, ready to serve ARP operations to the GUI client.

### Launch the GUI and cut a host

```bash
sudo python3 client/tuxcut.py
```

Outcome: the GUI lists every live host on the LAN with IP and MAC; selecting a host and choosing Cut ARP-spoofs it against the gateway so its traffic stops flowing, Restore reverses it, and the Protect option hides your machine and counters other spoofers on the segment — all executed by the daemon underneath.

*No corpus evidence — derived from upstream documentation.*
