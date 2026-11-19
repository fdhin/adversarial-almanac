---
name: "kismet"
categories:
  - 802-11
version: "2025_09_R1-4"
homepage: "https://www.kismetwireless.net/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-04-03"
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "kismetwireless/kismet"
github-stars: 2256
github-pushed: "2026-11-07"
github-archived: false
github-issues: 207
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1308
gh-activity-checked: "2026-11-09"
---

# kismet

> Passive Wi-Fi (and more) scanner, sniffer and IDS with a web UI.

## What it's for

Kismet passively detects networks and devices: it channel-hops across
Wi-Fi (and, with the right hardware, Bluetooth, RTL-SDR, 802.15.4 and
other PHYs), maps access points and clients, exposes hidden networks,
and records everything to its kismetdb logs. Modern Kismet (2020+) is a
single `kismet` binary that serves a browser UI and a REST API on port
2501 — the old kismet_server/kismet_client curses workflow no longer
exists, so older GUI clients and netxml-era converters only apply to
classic logs (see [[kismon]], [[kismet2earth]], [[kismet-earth]]). It
does not crack anything; it is the detection/collection half you pair
with crackers (handshakes captured here feed aircrack-ng). Log
conversion utilities such as kismetdb_to_pcap and kismetdb_to_kml ship
in the kismet-logtools package or when building Kismet from source.

## Typical use cases

### Start Kismet and configure from the browser

Run the server bare and set up sources through the web interface.

```
kismet
```

Outcome: the server starts and listens on http://localhost:2501 — on
first run you must set the admin login there (saved to
`~/.kismet/kismet_httpd.conf`); until a data source is added via the
Datasources panel no packets are captured.

### Start with a capture interface

Launch directly against a wireless interface.

```
kismet -c wlan0
```

Outcome: wlan0 is configured as a monitor-mode capture source during
startup, Kismet channel-hops and populates the web UI with the devices
seen, and kismetdb logs accumulate in the launch directory (set
`log_prefix` in the config to log elsewhere).

*No corpus evidence — derived from upstream documentation.*
