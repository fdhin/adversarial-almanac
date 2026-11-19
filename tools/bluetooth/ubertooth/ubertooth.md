---
name: "ubertooth"
categories:
  - blackarch-bluetooth
version: "2020.12.R1.r35.gc9dfdbd"
homepage: "https://github.com/greatscottgadgets/ubertooth"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-03-29"
build-system: python
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "greatscottgadgets/ubertooth"
github-stars: 2156
github-pushed: "2026-11-05"
github-archived: false
github-issues: 53
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# ubertooth

> Host tools for the Ubertooth One/Zero 2.4 GHz board: passive BLE sniffing, Classic Bluetooth discovery and decoding, raw capture, and spectrum analysis.

## What it's for

Project Ubertooth is an open-source 2.4 GHz development platform for Bluetooth experimentation; this package is the host-side command-line toolset that drives the board over USB. The tools passively sniff BLE (Bluetooth Smart) traffic — including following live connections — discover and decode Basic Rate (Classic) Bluetooth piconets, dump raw symbol streams, and emit signal-strength data for spectrum analysis. They require the physical Ubertooth hardware and a version-matched libbtbb; without a board attached, only device enumeration such as `ubertooth-util -N` completes. It is a passive monitoring platform: firmware for injection exists, but the shipped host tools focus on observation.

## Typical use cases

### Verify the board and firmware

```
ubertooth-util -v
```

Outcome: prints the firmware revision of the attached board, confirming it is flashed and responsive before capture. `ubertooth-util -N` counts attached Uberteeth, `-U<0-7>` selects one when several are connected, and `-r` performs a full reset.

### Capture BLE traffic to a pcap for Wireshark

```
ubertooth-btle -f -q ble.pcap
```

Outcome: follows BLE connections from their advertisements and writes packets to ble.pcap in PCAP format (DLT_BLUETOOTH_LE_LL_WITH_PHDR), openable directly in Wireshark; stop with Ctrl-C. The docs show the same flags piped into a Wireshark FIFO (`-f -q /tmp/pipe`) for live decoding.

### Sniff active BLE connections promiscuously

```
ubertooth-btle -p
```

Outcome: instead of waiting for a new connection to start, the tool hops across already-active connections and decodes whichever it locks onto — the mode for joining traffic already in progress.

### Follow only a specific BLE target

```
ubertooth-btle -t aa:bb:cc:dd:ee:ff
```

Outcome: connections involving the given address are followed until `-tnone` is passed or the board is reset, filtering out unrelated devices in busy 2.4 GHz environments.

### Survey Classic Bluetooth piconets

```
ubertooth-rx -z -t 20
```

Outcome: survey mode — passively collects every LAP and UAP pair visible for 20 seconds and lists the discovered piconets. A plain `ubertooth-rx` sniffs all LAPs indefinitely; `ubertooth-rx -l <lap> -u <uap>` fixes one piconet and attempts to calculate its clock and hop along with it, and `-r capture.pcapng` writes the decode to a PcapNG file.

### Watch the 2.4 GHz spectrum live

```
ubertooth-specan -G -q | feedgnuplot --stream 0.5 --domain --3d
```

Outcome: a continuous stream of per-channel signal strengths rendered by feedgnuplot as a live 3D plot of the band — a quick read on interference, occupancy, or jamming before starting Bluetooth work.

*No corpus evidence — derived from upstream documentation.*
