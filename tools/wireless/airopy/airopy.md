---
name: "airopy"
categories:
  - blackarch-wireless
version: "5.b83f11d"
homepage: "https://github.com/Josue87/Airopy"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-02"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Josue87/Airopy"
github-stars: 49
github-pushed: "2019-08-28"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# airopy

> Scapy-based passive sniffer that lists 802.11 access points and client stations on a monitor interface.

## What it's for

airopy is a small Python 3 helper (packaged as a wrapper that runs `airopy.py` from `/usr/share/airopy`) that listens on a monitor-mode interface and builds live, color-coded tables of what it sees: access points from beacons and probe responses, and client stations from their traffic, with each device's vendor resolved from its MAC OUI. It is a lightweight alternative to airodump-ng from the [[aircrack-ng]] suite for the discovery step only — it does not inject, deauthenticate, capture handshakes, or crack anything; it only watches. Scapy does the capture (root and a monitor interface are required), the channel can be fixed or hopped across 1–13 (1–11 with `--america`), and output can be filtered to devices from a specific vendor (Google, Apple, Microsoft, Samsung, Huawei, LG, Xiaomi, Intel). It is based on Dan McInerney's wifijammer codebase but omits the jamming.

## Typical use cases

### List the access points in range

Enumerate APs heard on the interface.

```
sudo airopy -i wlan0mon --aps
```

Outcome: channel-hops across the band, printing each discovered access point as it appears — ESSID, BSSID, channel, and vendor — in a live colorized table; press Ctrl-C to stop and keep the accumulated list on screen.

### List client stations on a fixed channel

Watch the clients associated to a target channel.

```
sudo airopy -i wlan0mon --stations -c 6
```

Outcome: locks the interface to channel 6 (`-c`) and lists the client stations heard there with their MACs and vendors — the set of devices to target later with deauthentication or capture tools.

### List APs and stations filtered to one vendor

Narrow discovery to a specific manufacturer's devices.

```
sudo airopy -i wlan0mon --aps --stations -d 1
```

Outcome: shows both access points and stations, but only those whose MAC OUI belongs to the selected vendor — `-d 0` Google, `1` Apple, `2` Microsoft, `3` Samsung, `4` Huawei, `5` LG, `6` Xiaomi, `7` Intel. Add `--america` when operating under FCC rules so the channel hopper only covers channels 1–11.

*No corpus evidence — derived from upstream documentation.*
