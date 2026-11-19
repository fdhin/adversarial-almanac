---
name: "spectools"
categories:
  - blackarch-wireless
version: "3.0"
homepage: "https://www.kismetwireless.net/spectools/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-12-22"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/spectools"
github-stars: 0
github-pushed: "2025-12-22"
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

# spectools

> Spectrum-Tools — libusb driver, GTK grapher and network server for the MetaGeek Wi-Spy USB spectrum analyzer.

## What it's for

Spectrum-Tools (written 2007-10 by Michael Kershaw / dragorn of the
Kismet project) is a set of utilities for the Wi-Spy USB spectrum
analyzer hardware from MetaGeek: userspace drivers over libusb, a
GTK/Cairo graphing UI offering planar (average/peak/current), spectral
(waterfall) and topographic views, a network server that shares a
sensor over TCP with broadcast announcement, and a raw numeric tool
for scripting. It shows RF energy across the bands the Wi-Spy covers —
useful for locating interference, not for decoding traffic. Real Wi-Spy
hardware is required: an ordinary 802.11 card cannot substitute. The
packaged 3.0 is a maintained fork (Obsidian-Covenant) that ports the
2009-era code to libusb 1.0 and GTK4; the project's original home is
kismetwireless.net/spectools/.

## Typical use cases

### List attached analyzers and their sweep ranges

Confirm the device is detected before starting anything else.

```
spectool_raw -l
```

Outcome: every detected Wi-Spy device with its name and supported
frequency ranges, then exit — no sweep data is streamed.

### Print a continuous stream of spectral samples

Text-mode spectrum for terminals and scripts.

```
spectool_raw
```

Outcome: repeated amplitude-per-frequency sweep data on stdout until
interrupted. Point it at a remote sensor instead with
`spectool_raw -n tcp://198.51.100.10:<port>`, or auto-discover one on
the LAN with `-b`.

### Graph the spectrum interactively

The full analyzer view.

```
spectool_gtk
```

Outcome: a window opens with the live Wi-Spy sweep plotted; switch
between the planar, spectral (waterfall) and topographic graphs, place
markers on frequencies, and monitor several devices (local USB or
network sources) side by side.

### Share an analyzer over the network

Serve a locally attached Wi-Spy to other machines.

```
spectool_net_server -b 5
```

Outcome: a TCP server that announces itself by broadcast every 5
seconds; `spectool_raw -b` or `spectool_gtk` on another host picks up
the announcement and plots the remote sensor. `-p` selects an
alternate port, `-a` binds a specific address.

*No corpus evidence — derived from upstream sources (README, Makefile.am program list and the usage text in spectool_raw.c / spectool_net_server.c of the packaged Obsidian-Covenant 3.0 source; original author credits in the same README).*
