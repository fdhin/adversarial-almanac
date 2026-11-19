---
name: "wificurse"
categories:
  - blackarch-wireless
version: "0.3.9"
homepage: "https://github.com/oblique/wificurse"
packaged-by: [blackarch, aur]
blackarch-builddate: "2018-11-12"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "oblique/wificurse"
github-stars: 102
github-pushed: "2013-10-30"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wificurse

> Minimal C Wi-Fi jammer that deauthenticates every access point it hears, one channel at a time.

## What it's for

wificurse, by oblique (2012–2013, upstream archived), is a small C tool that performs a blunt Wi-Fi denial of service: it listens for beacons, and for each access point heard it sends deauthentication frames using the broadcast MAC as the source, knocking every client off that AP for roughly 30 seconds before moving on. You hand it a raw wireless interface — it puts the card into monitor mode itself and hops channels 1–14 automatically, or a channel list you supply. It is strictly a monitor/injection attack tool: nothing here cracks keys, and deauthentication only disconnects clients temporarily. Its age shows — upstream is archived since 2013 and it depends on injection-capable drivers of that era.

## Typical use cases

### Jam every access point in range

Hand it your wireless card and let it sweep the whole band.

```
sudo wificurse wlan0
```

Outcome: switches `wlan0` to monitor mode, hops channels 1–14 listening for beacons, and attacks each AP found with broadcast-source deauthentication frames (~30 seconds per AP) until interrupted; a console view tracks the APs being attacked.

### Limit the attack to specific channels

Constrain the sweep to the channels you care about, using single values and ranges.

```
sudo wificurse -c 1,6,11 wlan0
```

Outcome: same beacon-triggered deauthentication, but only on channels 1, 6 and 11; range syntax like `1-6` is accepted, and channels with no AP are skipped after one second.

### List network interfaces

Check the interface name before starting.

```
wificurse -l
```

Outcome: prints all network interfaces on the system and exits — use it to pick the wireless card to pass as the interface argument.

*No corpus evidence — derived from upstream documentation (repo README and `src/wificurse.c` usage).*
