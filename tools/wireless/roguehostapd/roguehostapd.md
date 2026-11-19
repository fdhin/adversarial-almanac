---
name: "roguehostapd"
categories:
  - blackarch-wireless
version: "1.0.r45.g381b373"
homepage: "https://github.com/wifiphisher/roguehostapd"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-04"
build-system: other
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "wifiphisher/roguehostapd"
github-stars: 189
github-pushed: "2021-04-27"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# roguehostapd

> Fork of hostapd with KARMA support and Python ctypes bindings — the rogue-AP engine built for and driven by wifiphisher.

## What it's for

roguehostapd is hostapd (the standard user-space software access point) with two additions: attack features — most importantly KARMA, answering probe requests for any SSID a client asks for so the client auto-connects to your AP — and a Python interface via ctypes, so an orchestrator can spawn and reconfigure access points programmatically instead of editing config files. It was developed as the AP backend of the [[wifiphisher]] evil-twin framework, which uses it to stand up the rogue network that victims associate with; used standalone, `run.py` is the quick way to bring up an open, WPA2 or KARMA-enabled AP from one command line, and the Python `Hostapd()` class is the way to embed it. Development stopped in 2021; it tracks the hostapd of that era and expects the usual requirements — root, a wireless card supporting AP mode, and a monitor-capable driver for the attack features.

## Typical use cases

### Spawn an open rogue AP

Bring up a plain open access point for testing association behavior.

```
python run.py -i wlan0 -ssid OfficeNet
```

Outcome: starts hostapd on wlan0 broadcasting OfficeNet on channel 6 with no encryption; associated clients appear in the debug output, and the AP runs until interrupted — the baseline before adding attack features.

### Spawn a WPA2 rogue AP

Run the AP with a known passphrase, e.g. to compare handshake behavior.

```
python run.py -i wlan0 -ssid OfficeNet -pK Password1
```

Outcome: same AP but WPA2/WPA-protected with the given passphrase — clients must complete a full handshake to join, which is the configuration evil-twin attacks use when the victim expects an encrypted network; `-c` picks a different channel and `-bI` tunes the beacon interval.

### Enable the KARMA attack

Answer every probe so auto-connecting clients come to you.

```
python run.py -i wlan0 -ssid OfficeNet -kA
```

Outcome: the AP responds to probe requests for any SSID a nearby client is looking for, so devices with the real network saved associate with yours automatically — the feature roguehostapd exists for; `-dV` turns on verbose hostapd logging to watch the probes and associations happen.

### Drive an AP from Python

Embed rogue AP control in an orchestrator.

```
python -c "
from roguehostapd import hostapd_controller
ap = hostapd_controller.Hostapd()
ap.start({'ssid': 'OfficeNet', 'interface': 'wlan0', 'karma_enable': 1},
         {'debug_verbose': True})
"
```

Outcome: loads the compiled hostapd shared library over ctypes and starts the AP in a thread with a KARMA-enabled config supplied as a Python dict — the interface wifiphisher uses internally — and the returned object's `stop()` tears the AP down again; config keys mirror hostapd.conf (`ssid`, `interface`, `channel`, `wpa_passphrase`, `karma_enable`, ...).

*No corpus evidence — derived from upstream documentation.*
