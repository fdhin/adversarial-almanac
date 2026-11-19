---
name: "wig-ng"
categories:
  - wireless
version: "0.1-0parrot1"
homepage: "https://github.com/6e726d/wig-ng"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "6e726d/wig-ng"
github-stars: 40
github-pushed: "2022-11-28"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wig-ng

> Fingerprints Wi-Fi devices from vendor-specific protocol elements (WPS, Wi-Fi Direct, Cisco CCX, AWDL, HP printer IEs).

## What it's for

WIG (WiFi Information Gathering) is a passive device fingerprinting utility: instead of identifying access points by SSID, it decodes the protocol-specific information elements and management frames a device emits — WiFi Protected Setup, WiFi-Direct (P2P), Cisco Client Extension (CCX), Apple Wireless Direct Link (AWDL) and a proprietary HP-printer IE — to characterise the device type, capabilities and vendor. It accepts a monitor-mode interface for live capture or pcaps offline (a single file or a whole directory, processed in parallel), and an active mode enables frame injection for the modules that support it. It does not perform channel hopping — run it on a fixed channel or combine it with a channel-hopping capture tool. Despite the name, it is not a WiGLE.net client; "WIG" stands for WiFi Information Gathering.

## Typical use cases

### Fingerprint devices from a live monitor capture

Put a monitor interface up and fingerprint everything in range on that channel.

```
sudo iwconfig wlan0mon mode monitor && sudo ifconfig wlan0mon up
sudo wig-ng.py -i wlan0mon
```

Outcome: prints a per-device fingerprint derived from the WPS/P2P/CCX/AWDL/HP information elements seen in each device's frames; add `-v` (repeatable) for progressively more detail per device.

### Batch-fingerprint a directory of pcaps

Process an existing capture corpus instead of listening live.

```
wig-ng.py -R ./captures/ -c 4
```

Outcome: analyses every pcap file under `./captures/`, up to 4 files processed concurrently (`-c`), and reports the fingerprintable devices found in each; `-r capture.pcap` does the same for a single file.

### Run modules that inject frames

Enable active mode for the fingerprinting modules that send frames rather than only listening.

```
sudo wig-ng.py -i wlan0mon -a
```

Outcome: the injection-capable modules (for example those probing WPS or P2P behaviour) become active alongside the passive ones; requires root and a monitor interface that supports injection.

*No corpus evidence — derived from upstream documentation.*
