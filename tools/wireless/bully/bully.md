---
name: "bully"
categories:
  - 802-11
version: "1.4.00-1"
homepage: "https://github.com/kimocoder/bully"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-05-27"
build-system: make
meta-checked: "2026-11-09"
github-repo: "kimocoder/bully"
github-stars: 101
github-pushed: "2026-06-22"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# bully

> WPS PIN brute-force attack in C — a leaner reaver successor that recovers the PIN and then the WPA passphrase of WPS-enabled access points.

## What it's for

bully exploits the well-known design flaw in the WPS specification: it recovers a target access point's WPS PIN through the M5/M7 exchange and, with the PIN known, extracts the WPA passphrase. Written originally by Brian Purcell (2012) and continued by the aircrack-ng team (wiire, kimocoder), it needs only libpcap, runs on embedded Linux (OpenWrt) regardless of architecture, and handles anomalous situations better than the original reaver — lockout waiting and detection, checksum-digit brute force, sequential or randomized PIN order. It can also hand captured nonces/hashes to pixiewps for an offline PixieDust attempt. It requires root, a monitor-mode interface capable of injection, and a WPS-enabled AP that does not rate-limit you into the ground; PIN and session state persist under ~/.bully so interrupted attacks resume.

## Typical use cases

### Run the WPS attack against a target AP

```
bully -b 00:11:22:33:44:55 -c 6 wlan0mon
```

Outcome: starts the online PIN attack against the given BSSID on channel 6 through the monitor interface; on success the WPS PIN and the derived WPA passphrase are printed, and session state is stored under ~/.bully so a rerun resumes where it stopped. `-e essid` works instead of `-b` when the BSSID is unknown.

### Control PIN ordering and checksum handling

```
bully -b 00:11:22:33:44:55 -c 6 -S -B wlan0mon
```

Outcome: `-S` tries PINs sequentially instead of randomized order, and `-B` brute-forces the checksum digit rather than computing it — the slower fallback for APs that reject computed checksums; a starting PIN or pin index can be set with `-p`/`-i`.

### Keep running against WPS lockouts

```
bully -b 00:11:22:33:44:55 -c 6 -l 60 -D wlan0mon
```

Outcome: waits 60 seconds whenever the AP reports a WPS lock (default 43) and `-D` detects lockouts the AP does not report, keeping the attack alive against rate-limiting targets; `-L` ignores reported locks entirely, and `-c 1,6,11` reacquires an AP that changes channels.

### Attempt the offline PixieDust attack

```
bully -d -b 00:11:22:33:44:55 -c 6 wlan0mon
```

Outcome: captures the WPS exchange and attempts to use pixiewps to derive the PIN offline from the AP's nonces and hash — instant against vulnerable chipsets, harmless to try before committing to the long online attack.

*No corpus evidence — derived from upstream documentation.*
