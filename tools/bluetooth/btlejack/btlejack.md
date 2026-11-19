---
name: "btlejack"
categories:
  - blackarch-bluetooth
version: "v2.1.1.r1.gc487859"
homepage: "https://github.com/virtualabs/btlejack"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-17"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "virtualabs/btlejack"
github-stars: 2123
github-pushed: "2024-08-04"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# btlejack

> Micro:Bit-based Bluetooth Low Energy swiss-army knife: sniffs, jams and hijacks BLE connections.

## What it's for

btlejack turns one or more BBC Micro:Bit boards (also Adafruit Bluefruit LE sniffers and nRF51822 boards) flashed with its firmware into a Bluetooth Low Energy capture and attack platform: enumerate live connections by access address, resynchronize onto and follow one, capture new connections as they form, jam them, or hijack a connection and interact with the peripheral from an interactive prompt. It handles BLE 4.x and — limited to the 1 Mbps uncoded PHY and without channel-map updates — BLE 5.x CSA #2 connections, and exports captures to PCAP formats that Wireshark and crackle understand. It requires the hardware sniffers; it is not a plain host-radio sniffer.

## Typical use cases

### Flash the sniffer firmware

Prepare attached Micro:Bit boards for the installed client version.

```
btlejack -i
```

Outcome: detects every connected Micro:Bit (mass-storage mount containing MICROBIT) and copies the matching firmware onto it; re-run after each btlejack upgrade so firmware and client stay in sync.

### Enumerate live connections

Discover existing BLE connections in range.

```
btlejack -s
```

Outcome: lists each live connection as `[ -54 dBm] 0xcd91d517 | pkts: 2` — signal strength, 32-bit access address, and packet count; pick the access address with the strongest signal and most packets to follow.

### Follow and capture an existing connection

Resynchronize onto a connection and dump its traffic.

```
btlejack -f 0xdda4845e -o capture.pcap
```

Outcome: recovers CRCInit, channel map and hop parameters, then streams the link's LL Data packets; with `-o` the capture is written to a PCAP (`-x nordic` or `-x ll_phdr` select other DLT formats — `ll_phdr` output also feeds crackle for cracking encrypted connections).

### Capture new connections

Wait for a device to connect and record the link from its first packet.

```
btlejack -c any
```

Outcome: on the next CONNECT_REQ prints both devices' addresses with the connection's access address, CRCInit, hop interval/increment, channel map and timeout, then captures the link's traffic; replace `any` with a BD address to only capture connections involving that initiator.

### Hijack (or jam) a connection

Take over a live link.

```
btlejack -f 0x9c68fd30 -t -m 0x1fffffffff
```

Outcome: synchronizes and hijacks the connection, dropping you at a `btlejack>` prompt where `discover` enumerates services and characteristics, `read <handle>` reads a value handle, `write <handle> <hex|str> <data>` writes one, and `ll` sends raw link-layer PDUs; use `-j` instead of `-t` to simply jam the connection dead.

### Live-capture into Wireshark

Stream packets to a FIFO for real-time dissection.

```
btlejack -c any -w /tmp/blepipe
```

Outcome: writes the packet stream to the named FIFO so Wireshark reading the same path shows the session live; combine with `-o` to tee the capture into a PCAP file at the same time.

*No corpus evidence — derived from upstream documentation.*
