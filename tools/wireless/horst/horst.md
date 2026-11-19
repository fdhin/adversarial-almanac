---
name: "horst"
gentoo-category: "net-wireless"
version: "3.0"
homepage: "http://br1.einfach.org/tech/horst/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo, aur, nixpkgs]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "br101/horst"
github-stars: 424
github-pushed: "2024-01-10"
github-archived: false
github-issues: 14
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# horst

> Small ncurses 802.11 analyzer focused on signal strength, channel utilization and mesh/IBSS diagnostics rather than packet payloads.

## What it's for

`horst` ("Highly Optimized Radio Scanning Tool", by Bruno Randolf) is a lightweight text-UI wireless LAN analyzer for debugging WiFi with a monitor-mode card. Where Wireshark or Kismet go deep on packets, horst gives a live aggregated overview: per-station signal (RSSI) values, computed channel utilization, a spectrum-analyzer view of signal and usage per channel, a graphical packet history with rates and types, statistics per physical rate, and detection of IBSS "splits" (same SSID, different BSSID). It has dedicated support for Ad-hoc and mesh networks (OLSR, batman) and can run as a headless server on a remote node with a connected client — a common setup on mesh routers. It does not do packet injection or capture-to-file analysis; it is the tool for "what is going on in the air here".

## Typical use cases

### Live overview of a channel

```
sudo horst -i wlan0
```

Outcome: opens the ncurses display showing all observed stations, their signal levels, packet types and rates on the current channel; since version 5.0 horst also sets the interface into monitor mode itself. To scan across channels instead, use `-s` (channel scanning/hopping).

### Remote monitoring from an embedded node

```
horst -i wlan0 -N -q
```

Outcome: runs horst on the wireless node as a headless server (no UI) exporting the display over the network — then from your workstation run `horst -n 198.51.100.10` to attach and watch the radio environment of the remote node live (one client at a time).

### Prepare an interface manually for channel control

```
sudo ifconfig wlan0 down
sudo iw wlan0 set type monitor
sudo rfkill unblock all
sudo ifconfig wlan0 up
sudo iw wlan0 set channel 6
```

Outcome: puts the card fully into monitor mode on channel 6 so horst (or `horst -s`) can change channels freely — while the main interface is busy as a client or AP, the driver locks the channel and horst cannot hop.

*No corpus evidence — derived from upstream documentation (run modes verified against the upstream README usage notes).*
