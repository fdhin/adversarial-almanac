---
name: "hwk"
categories:
  - blackarch-wireless
version: "0.4"
homepage: "http://www.nullsecurity.net/"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# hwk

> NullSecurity's 2013 two-piece 802.11 toolkit: `hawk` floods the air with management frames, `eagle` crafts raw radiotap/WLAN/MGT/LLC packets field by field.

## What it's for

hwk (by atzeton of team NullSecurity, 2013, C + libpcap) bundles two complementary tools for raw 802.11 work. `hawk` is the flooding half: it opens an interface, optionally hops channels while scanning, and floods deauthentication (its default "multi deauth check") or authentication frames at a chosen AP/client pair. `eagle` is the crafting half: it assembles a single packet bottom-up — radiotap header, WLAN frame-control/duration/addresses, management-frame fixed and tagged parameters (beacon interval, capability, information elements), LLC/SNAP header fields, and an optional random payload — and sends it at a chosen rate. It is beta-quality 2013 code against long-obsolete drivers, so treat it as a minimal reference implementation of frame crafting rather than a modern attack tool (aircrack-ng's packetforge-ng and mdk4 cover the same ground maintained). Note the packaging installs the crafting binary as `hwk-eagle` to avoid a filename clash, while `hawk` keeps its name. Beware same-named GitHub projects that are unrelated (a Haskell text-processing tool shares the name).

## Typical use cases

### Flood an AP with deauthentication frames

```
sudo hawk --iface mon0 --channel 6 --bssid 00:11:22:33:44:55
```

Outcome: opens mon0 locked to channel 6 and runs the default multi-deauth flood against the given BSSID, disconnecting its clients; `--delay` (microseconds between packets) sets the send rate and `--dest` overrides the destination address (defaults to the BSSID).

### Target one client of an AP

```
sudo hawk --iface mon0 --channel 6 --bssid 00:11:22:33:44:55 --client aa:bb:cc:dd:ee:ff
```

Outcome: the flood is aimed at the single client MAC instead of everyone associated — swap the default behavior for `--auth` to send authentication frames instead of deauthentications, which probes how an AP handles auth floods.

### Craft and send a raw management frame

```
sudo hwk-eagle --iface mon0 --channel=11 --rtap --wlan.dst=ff:ff:ff:ff:ff:ff --wlan.src=00:11:22:33:44:55 --wlan.bssid=00:11:22:33:44:55 --mgt.tagged.count=1 --mgt.tagged.id=0x00 --llc.type=0x888e --payload.len=64
```

Outcome: assembles one packet from the given radiotap header (`--rtap`), WLAN addresses, a single tagged parameter with tag ID 0x00 (SSID element), an LLC header typed 0x888e (802.1X) and 64 bytes of random payload, then transmits it on channel 11 — every layer is independently configurable through the `--rtap.*`, `--wlan.*`, `--mgt.*`, `--llc.*` and `--payload.len` options; run with `--help` to enumerate all fields.

*No corpus evidence — derived from the packaged source (release tarball in the [nullsecuritynet tools collection](https://github.com/nullsecuritynet/tools) under `wireless/hwk`, which the frontmatter links); flags verified against the bundled help text.*
