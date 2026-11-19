---
name: "morxtunel"
categories:
  - blackarch-tunnel
version: "1.0"
homepage: "http://www.morxploit.com/tools/"
packaged-by: [blackarch]
blackarch-builddate: "2018-10-03"
build-system: other
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# morxtunel

> Single-file C tool by Simo Ghannam (MorXploit Research, 2014) that builds a TUN or TAP point-to-point tunnel over one TCP connection — interfaces are created at launch and vanish on exit.

## What it's for

A dependency-free layer-2/layer-3 tunnel between two peers. One peer runs as server (`-S`, listens on a TCP port), the other as client (`-C server-ip`); each creates its own TUN or TAP device named and addressed from the command line (`-c name`, `-I addr/mask`), and frames crossing the devices are relayed over the single TCP connection — no pre-created interfaces, no OpenVPN-style plumbing, and the devices are removed automatically when the program exits. There is no encryption or authentication: treat it as a transport to run SSH or a real VPN over. The original morxploit.com distribution is only reachable through web archives; the BlackArch package compiles the archived `MorXTunel.c` directly and installs it as `morxtunel`.

## Typical use cases

### Start the server end

```
sudo morxtunel -c lt1 -t tun -I 1.2.3.4/24 -p 1111 -u user -S -v
```

Outcome: creates the `lt1` TUN device configured 1.2.3.4/24, prints `[+] listening on port 1111` and waits for the client. `-v` adds per-packet verbose output; `-u` sets the device owner so the peer's traffic arrives to that user.

### Connect the client end

```
sudo morxtunel -c lt2 -t tun -I 1.2.3.9/24 -p 1111 -u user -C 198.51.100.10 -v
```

Outcome: creates `lt2` with an address inside the same /24 as the server's tunnel IP and connects to 198.51.100.10:1111; `ping 1.2.3.4` and `ssh user@1.2.3.4` now traverse the tunnel. Both tunnel interfaces disappear automatically when either side exits.

### Carry Ethernet frames instead of IP

Run the same pair with `-t tap`:

```
sudo morxtunel -c ltap1 -t tap -I 1.2.3.4/24 -p 1111 -u user -S
sudo morxtunel -c ltap2 -t tap -I 1.2.3.9/24 -p 1111 -u user -C 198.51.100.10
```

Outcome: the devices operate at layer 2 (TAP), so Ethernet-level traffic — non-IP protocols, bridging on either end — crosses the link rather than only IP packets.

*No corpus evidence — derived from upstream documentation.*
