---
name: "stegosip"
categories:
  - blackarch-tunnel
version: "11.5cda6d6"
homepage: "https://github.com/epinna/Stegosip"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "epinna/Stegosip"
github-stars: 97
github-pushed: "2020-06-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stegosip

> Hides a point-to-point IP tunnel inside the RTP media stream of a live SIP call — steganographic covert channel that surfaces as a `stego0` network interface.

## What it's for

StegoSIP (developed for a Politecnico di Torino master's thesis on steganography over SIP/RTP) creates a hidden network between two hosts that are simply having a VoIP call. It watches SIP signalling on UDP 5060, spots the RTP media stream negotiated for a call, and smuggles IP packets inside that media: either by overwriting the least significant bits of RTP payload bytes (LSB injector) or by exploiting intentionally delayed audio packets (LACK injector). Both endpoints then get a `stego0` TUN interface — by default 10.0.0.1 and 10.0.0.2 — usable like any point-to-point link while the call is up. It is SIP-client agnostic (tested with Ekiga), runs in userspace on top of netfilter queues, and requires the same shared secret and injector settings on both ends. Its goal is invisibility inside legitimate-looking call media, not confidentiality: it does not provide its own encryption.

## Typical use cases

### Configure both endpoints for the same hidden link

```
# host A: /usr/share/stegosip/stegosip.conf
tun_ip = 10.0.0.1
injector = LACK
secret = Password1
```

Outcome: sets this endpoint's covert address (10.0.0.1), the steganographic injector and the shared secret; mirror the file on host B with `tun_ip = 10.0.0.2` and identical injector/secret values. `injector` also accepts `LSB` (with `modified_bit_per_byte`) and `raw`, each with its own effective MTU.

### Start it and let a call bring the tunnel up

```
sudo stegosip
```

Outcome: StegoSIP installs its dissector and netfilter rules on the SIP ports (`[SIP] added dissector and netfilter rules on udp 5060 ports.`); once a call is placed between the two endpoints it logs the call and SDP details, loads the RTP filters and injector (`[RTP] Injector 'LACK' module loaded`), and raises `stego0` on both hosts (MTU around 1392). The channel exists only while the call does.

### Use the covert link like any interface

```
ping 10.0.0.2
```

Outcome: ICMP packets travel steganographically inside the ongoing RTP stream and are answered from the peer's stego0 address; anything that tolerates the small interface MTU — SSH, light file transfers, scanning — works the same way over the hidden channel.

*No corpus evidence — derived from upstream documentation.*
