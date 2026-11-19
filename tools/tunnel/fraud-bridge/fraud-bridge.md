---
name: "fraud-bridge"
categories:
  - blackarch-tunnel
version: "rel.0.32s.r3.g88a5baa"
homepage: "https://github.com/stealth/fraud-bridge"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-20"
build-system: make
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "stealth/fraud-bridge"
github-stars: 234
github-pushed: "2025-04-04"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fraud-bridge

> Censorship-evasion tunnel by stealth (Solar Designer): a point-to-point IP link wrapped in ICMP, DNS or NTP packets, over IPv4 or IPv6.

## What it's for

Getting connectivity out of networks that block direct TCP or UDP. One instance runs *inside* the restricted network, another *outside* on a reachable server; together they bring up a point-to-point TUN device (default `tun1`, change with `-d`) whose packets are carried inside ICMP echo, DNS TXT replies (stuffed via EDNS0) or NTP packets. It patches the TCP MSS option for a non-fragmented stream, HMAC-protects tunnel packets with MD5 against injected traffic, and — because the TCP state stays in the kernels at both ends — survives roaming between local addresses (Wi-Fi to 5G) and even switching the carrier protocol (DNS to ICMP to NTP) under a live SSH session. It provides no confidentiality by itself: run SSH across the bridge, optionally with `-D` for a SOCKS proxy.

## Typical use cases

### Bring up an ICMP tunnel between inside and outside

```
fraud-bridge -i -k key                        # outside: stands by for the peer
fraud-bridge -i -R 198.51.100.10 -k key       # inside: dials the outside peer
```

Outcome: each side brings up the point-to-point tunnel on `tun1` with the default address pair 1.2.3.4 <-> 1.2.3.5; then run the bundled `outside.sh` / `inside.sh` helper scripts (shipped in the source tree) on the respective ends to finish the setup — they must be re-run after every restart of the daemon at that end. If plain ICMP echo is filtered, start with `-t 13` inside and `-t 14` outside to carry the tunnel in timestamp request/reply pairs instead.

### Tunnel through DNS using a domain

```
fraud-bridge -u -D tunnel.example.com -k key                  # outside
fraud-bridge -u -R 198.51.100.10 -D tunnel.example.com -k key # inside
```

Outcome: tunnel packets ride DNS TXT replies, packed with EDNS0 (default size 1024, `-E` — change it on both ends together). `-R` on the inside may point at the outside server's IP directly, or at your provider's recursive resolver if you control the domain's authoritative side; without `-L` (an explicit local bind address) the outside end simply listens. The README advises trying ICMP or NTP before DNS.

### Ride SSH and a SOCKS proxy across the bridge

```
ssh -D 1234 user@1.2.3.5
```

Outcome: an SSH session to the outside host through the tunnel, with a local SOCKS5 proxy on `:1234` — point a browser or any SOCKS-aware tool at it and the session runs across the bridge. 1.2.3.5 is the outside end of the default point-to-point pair.

*No corpus evidence — derived from upstream documentation.*
