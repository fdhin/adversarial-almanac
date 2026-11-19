---
name: "ufo-wardriving"
categories:
  - blackarch-cracker
version: "4"
homepage: "http://www.ufo-wardriving.com/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "awker/ufo-wardriving"
github-stars: 1
github-pushed: "2016-03-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ufo-wardriving

> PyQt4 wardriving GUI that computes vendor default WEP/WPA keys for consumer routers from their SSID and MAC address.

## What it's for

UFO Wardriving audits consumer wireless routers whose factory default keys are derived deterministically from the SSID and/or MAC address. Its GUI offers one tab per supported ISP or vendor — Alice/Telecom Italia AGPF, Fastweb, D-Link, Huawei, Infostrada, Jazztel, TeleTu/Ya.com, Tele2 — plus a wireless scanner view; entering an SSID/MAC pair observed on an access point yields the candidate default key(s) computed by that vendor's algorithm (the per-vendor decoders live in the packaged `core/` directory with their lookup data under `config/`). It is python2/PyQt4-era software aimed at 2010s ISP-issued CPE; it does not capture handshakes or crack arbitrary WPA networks, so reach for it when testing for factory-default credentials, not as a general Wi-Fi cracker.

## Typical use cases

### Launch the default-key decoder GUI

Run the packaged launcher.

```
ufo-wardriving
```

Outcome: opens the PyQt4 main window with per-vendor tabs and a wireless scanner tab; selecting a vendor tab (e.g. Alice) and entering the observed SSID and MAC address prints the candidate default WPA/WEP key(s) that router model would have shipped with, ready to be tested against the network.

*No corpus evidence — derived from upstream documentation.*
