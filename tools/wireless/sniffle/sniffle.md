---
name: "sniffle"
categories:
  - wireless
version: "git (2025-09-25)"
homepage: "https://github.com/nccgroup/sniffle"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "nccgroup/sniffle"
github-stars: 1199
github-pushed: "2025-09-25"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 31
---

# sniffle

> Sniffle — a Bluetooth 5 / 4.x LE sniffer built on TI CC26x2 / CC1352 LaunchPad hardware.

## What it's for

Sniffle (NCC Group) turns an inexpensive TI CC26x2R or CC1352
LaunchPad into a full Bluetooth Low Energy sniffer: custom firmware on
the board tracks advertising channels and follows connections, and
Python host tools decode what arrives over USB serial. It handles BT5
extended-length packets, both channel selection algorithms, all PHYs
(1M, 2M, coded/long-range) and BT5 extended (non-periodic)
advertising; it can capture advertisements from a target on all three
primary advertising channels at once, filter by MAC, IRK, name string
or RSSI, and export Ubertooth-compatible PCAP files for Wireshark. It
is receive-only — no packet injection — and it needs the LaunchPad
flashed with the Sniffle firmware first.

## Typical use cases

### Sniff all advertising traffic on one channel

Listen to everything advertisers send on a single advertising channel.

```
./sniff_receiver.py -s /dev/ttyACM0 -c 38 -r -50 -a
```

Outcome: decoded advertisement packets printed live, restricted to
channel 38 and to RSSI at or above -50 dBm; `-a` keeps the sniffer on
the advertising channel even when CONNECT_REQs appear
(advertisements-only mode).

### Follow one device and capture it to PCAP

Record a target's advertisements and the connection it accepts.

```
./sniff_receiver.py -m 12:34:56:78:9A:BC -o capture.pcap
```

Outcome: the sniffer locks onto the given MAC, follows it through the
connection (channel hops, length/PHY/channel-map changes), and writes
the traffic to `capture.pcap` for analysis in Wireshark.

### Enumerate nearby advertisers

Scanner mode: a ranked census of BLE devices.

```
./scanner.py -d
```

Outcome: a periodically refreshed list of advertising devices sorted
by RSSI in descending order, with decoded advertisement data; add
`-o scan.pcap` to also write the raw advertisements to a PCAP file.

*No corpus evidence — derived from upstream documentation (README: `sniff_receiver.py` and `scanner.py` help output with usage examples, hardware list, feature set).*
