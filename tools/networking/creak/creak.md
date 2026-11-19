---
name: "creak"
categories:
  - blackarch-networking
version: "41.a6c011f"
homepage: "https://github.com/codepr/creak"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "codepr/creak"
github-stars: 129
github-pushed: "2023-08-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# creak

> ARP-poisoning MITM script: deny a host's traffic, spoof DNS, hijack sessions, spoof your MAC.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Creak (by codepr) bundles several classic man-in-the-middle attacks on local-network hosts into one Python CLI. Its main trick is ARP poisoning a target to deny navigation and downloads — it sends reset TCP packets for every request the target makes to the router — plus DNS spoofing (answering chosen hostnames with a redirection of your choosing), an old TCP session-hijack-by-desynchronization mode, and MAC spoofing with either a random address or a vendor-prefix-based one. Reach for it in a lab to demonstrate or test these legacy LAN attacks; it is a didactic project, needs root and raw-packet libraries (pcap/dpkt, libdnet for DNS spoofing), and does not provide modern TLS-downgrade or credential-capture capability.

## Typical use cases

### Cut a target off from the network

Demonstrate a denial-of-service via ARP poisoning.

```
creak -t 198.51.100.30 eth0
```

Outcome: poisons the target's ARP path to the router and sends TCP resets for every request, so the target's browsing and downloads fail until you stop the tool (run as root on `eth0`).

### Deny traffic through a specific gateway

Target a router other than the default one.

```
creak -s 198.51.100.2 -t 198.51.100.30 eth0
```

Outcome: same poison-and-reset attack, but positions 198.51.100.2 as the source/gateway address instead of the detected default route.

### Spoof DNS answers for a victim

Redirect a chosen hostname to a host you control.

```
creak -t 198.51.100.30 -a www.example.com -r redirect.example.net eth0
```

Outcome: while poisoned, the target's requests for www.example.com are answered with redirect.example.net; requires the libdnet module.

### Attack with a spoofed MAC address

Avoid exposing your real interface address.

```
creak -x -t 198.51.100.30 eth0
```

Outcome: generates a fake MAC used for the attack; add `-M <manufacturer>` to base the prefix on a specific vendor's OUI, or `-m AA:BB:CC:DD:EE:FF` to supply an entire address.

### Hit multiple targets at once

Take several hosts offline in one run.

```
creak -x -t 198.51.100.30 -t 198.51.100.31 -t 198.51.100.32 eth0
```

Outcome: `-t` can be repeated; every listed address gets the same poisoning and reset treatment in a single session.
