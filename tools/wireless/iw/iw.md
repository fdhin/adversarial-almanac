---
name: "iw"
categories:
  - 802-11
version: "6.17-1"
homepage: "https://wireless.wiki.kernel.org/en/users/documentation/iw"
origin: "arch"
arch-repo: "core"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2025-09-03"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# iw

> nl80211-based tool for inspecting and configuring Linux wireless devices.

## What it's for

iw is the standard Linux command-line utility for talking to Wi-Fi
drivers through the nl80211 kernel interface: it lists interfaces and
radios, reports link state, triggers scans, switches interfaces between
managed, monitor, AP and mesh modes, and sets channels and regulatory
domains. It replaces the old wireless-tools commands (iwconfig, iwlist,
iwpriv). It does not capture or crack traffic by itself — put the card
into monitor mode with iw, then hand the interface to a sniffer or
cracker.

## Typical use cases

### List the system's wireless interfaces

See every wireless device and its basic state before doing anything else.

```
iw dev
```

Outcome: one stanza per interface showing its name (`wlan0` …), MAC
address, type (managed, monitor, …) and channel — enough to pick the
right interface for the commands that follow.

### Show the current association

Check which network an interface is connected to and how strong the link is.

```
iw dev wlan0 link
```

Outcome: prints `Connected to <BSSID>`, the SSID, frequency, signal in
dBm and the negotiated TX bitrate — the quickest way to read the
BSSID/SSID/signal when scoping a wireless target.

### Inspect an interface's configuration

Read the mode and channel an interface is currently operating in.

```
iw dev wlan0 info
```

Outcome: fields including the interface address, type (managed vs
monitor), wiphy index, channel and channel width — confirm monitor mode
and the channel before starting a capture.

### Scan for nearby networks

Discover access points and their advertised capabilities.

```
iw dev wlan0 scan
```

Outcome: a `BSS <bssid>` block per network with SSID, signal,
frequency, capability and information elements — WPS presence,
RSN/WPA details and vendor IEs all show up here.

### Check radio capabilities

Verify what the hardware and driver actually support.

```
iw list
```

Outcome: per-radio (wiphy) details of supported bands, channels and
interface modes — look for `AP` in "Supported interface modes" before
building a rogue access point, and `monitor` before capturing.

### Create and remove a monitor interface

Give capture tools a dedicated monitor-mode interface without disturbing the managed one.

```
iw phy phy0 interface add moni0 type monitor
```

Outcome: a new `moni0` interface in monitor mode on phy0, ready for
airodump-ng or tcpdump; set its channel with
`iw dev moni0 set channel 6` and remove it again with
`iw dev moni0 del`.
