---
name: "gspoof"
categories:
  - blackarch-networking
version: "3.2"
homepage: "http://gspoof.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2019-06-16"
build-system: autotools
license: MIT
meta-checked: "2026-11-09"
github-repo: "embyte0/gspoof"
github-stars: 1
github-pushed: "2021-04-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gspoof

> Console and GTK+ tool for hand-crafting and sending TCP/IP packets, one header field at a time.

## What it's for

gspoof (Embyte, v3.2, 2003) is a C packet forger built on libnet for testing how hosts and middleboxes react to hand-made TCP traffic. It exposes the IP and TCP header fields — source/destination address, TTL, TOS (auto-derived from ECN parameters), ports, control flags, sequence and acknowledgement numbers, window, urgent pointer — plus an optional payload of up to 128 bytes, and can send a single crafted packet or a timed flood set. Two front-ends ship in one binary: an interactive console menu and a GTK+ GUI; a "Full mode" additionally allows editing the Ethernet header (source/destination MAC, ethernet type). It only sends packets — pair it with a sniffer to observe the effect. IPv4, Linux/BSD/macOS.

## Typical use cases

### Craft and send packets from the interactive console

```
sudo gspoof -c
```

Outcome: drops into a numbered console menu (type `help` for the command list) where source/destination IP and ports, TTL, flags, window, payload and ECN/TOS parameters are set interactively; each send transmits the crafted TCP packet, and the Options menu enables payload attachment and repeated "multi packet" sends with a delay.

### Build packets in the GTK+ interface

```
gspoof -g
```

Outcome: opens the GTK+ window with the same header fields as form entries; its Options menu adds data payloads, multi-packet sending, ECN configuration and the link-layer "Full mode" for Ethernet header control. Only available when compiled with GTK+ support — `-v` prints the running version, and without flags the program picks console mode unless GTK+ support is present.

*No corpus evidence — derived from upstream documentation.*
