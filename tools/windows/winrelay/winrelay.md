---
name: "winrelay"
categories:
  - blackarch-windows
version: "2.0"
homepage: "http://ntsecurity.nu/toolbox/winrelay/"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# winrelay

> TCP/UDP port forwarder for Windows with IPv4/IPv6 and source-address control.

## What it's for

WinRelay (Arne Vidstrom, ntsecurity.nu, v2.0) is a Windows command-line forwarder/redirector: it listens on an IP:port you choose and relays connections (TCP) or datagrams (UDP) to a destination IP:port, for both IPv4 and IPv6. You can also pin the source IP and source port used for the outgoing leg — the feature that made it a pentest staple alongside fpipe, because some pivots (chained border devices, port-restricted scenarios) only pass traffic from an expected source port. Typical role: run it on a compromised Windows box in the middle of a segmented network to bridge a path that wouldn't otherwise connect. It forwards fixed point-to-point; it is not a SOCKS proxy and does no encryption.

## Typical use cases

### Forward a local port to an internal host

The basic relay: expose an internal service on the pivot host's listener.

```
winrelay.exe -lip 0.0.0.0 -lp 81 -dip 198.51.100.20 -dp 80 -proto tcp
```

Outcome: WinRelay listens on all interfaces, TCP port 81, and forwards each accepted connection to `198.51.100.20:80` — clients connecting to the pivot host on 81 reach the internal web server.

### Relay with a pinned source port

Force the outgoing connection to use a specific source port and address where the path demands it.

```
winrelay.exe -lip 0.0.0.0 -lp 13389 -sip 198.51.100.10 -sp 53 -dip 198.51.100.20 -dp 3389 -proto tcp
```

Outcome: traffic to the pivot's 13389 is relayed to the target's RDP port with source address `198.51.100.10` and source port 53 — use this shape when an intermediary only forwards traffic matching an allowed source port; `-proto udp` switches the relay to UDP datagrams.

*No corpus evidence — derived from upstream documentation (ntsecurity.nu/vidstromlabs tool page) and the tool's own usage output (`winrelay [-lip <IP>] -lp <port> [-sip <IP>] [-sp <port>] -dip <IP> -dp <port> -proto tcp|udp`).*
