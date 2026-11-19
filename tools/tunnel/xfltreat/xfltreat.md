---
name: "xfltreat"
categories:
  - blackarch-tunnel
version: "270.17d4ec8"
homepage: "https://github.com/earthquake/xfltreat/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "earthquake/xfltreat"
github-stars: 331
github-pushed: "2020-01-16"
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

# xfltreat

> Modular tunnelling framework — client/server VPN-over-anything with switchable transport modules including ICMP, SOCKS, HTTP CONNECT and DNS.

## What it's for

XFLTReaT (Balazs Bucsay) is a framework for getting unrestricted network access out of (or into) a restricted environment, or for exfiltrating data over a noisy side channel. One process per side: the server runs on an unrestricted host, the client on the restricted one, and the whole tunnel is driven by a single INI-style config file that selects which transport modules are enabled — TCP and UDP generic, ICMP echo, SOCKS v4/4a/5, HTTP CONNECT, SCTP, WebSocket, DNS (A/CNAME, PRIVATE, NULL), and RDP on Windows. On connect the client gets a virtual interface (default `xfl0`, 10.9.0.101/24 to the server's 10.9.0.1) and routes traffic through whichever module both sides allow, with optional authentication (salted-SHA512 shared key) and encryption (basic/advanced) layers. Python 2.7, must run as root on both ends; the author's own disclaimer says edge cases are not production-hardened.

## Typical use cases

### Configure and start the server

```
sudo xfltreat -s
```

Outcome: reads `xfltreat.conf` (packaged under /usr/share/xfltreat) and starts every enabled module — TCP generic on port 1337 and UDP generic on 1338 by default — plus the `xfl0` server interface; before starting, set `remoteserverip` in `[Global]` to the address clients will dial.

### Connect the client and bring up the tunnel

```
sudo xfltreat -c
```

Outcome: the client contacts the configured server, negotiates the enabled transport, and raises its own `xfl0` interface (10.9.0.101/24 by default); traffic to the scope configured in the server's network now flows through the tunnel — the same command works for any enabled transport, since the config decides the carrier.

### Check which modules the server can offer

```
sudo xfltreat --check
```

Outcome: probes the server side and reports the modules it finds enabled, letting you pick the transport to enable on the client without trial and error.

### Route only chosen destinations through the tunnel

```
sudo xfltreat -c --split
```

Outcome: split-tunnelling mode forwards only the addresses/CIDRs listed in the scope file (`scope = misc/scope.txt` in the config) instead of the client's whole traffic — lighter on the carrier channel and less visible in traffic volume.

### Switch the carrier to DNS

```
# xfltreat.conf
[DNS]
enabled = yes
hostname = tunnel.example.com
zonefile = misc/zonefile
```

Outcome: with the DNS module enabled (and the delegated zone's zonefile in place on the server), tunnel data crosses as A/CNAME, PRIVATE or NULL record exchanges for `tunnel.example.com` — the classic fallback when only DNS egress survives; use `--config` to point at an alternate conf file while experimenting.

*No corpus evidence — derived from upstream documentation.*
