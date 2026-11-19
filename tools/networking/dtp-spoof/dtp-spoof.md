---
name: "dtp-spoof"
categories:
  - blackarch-networking
version: "5.3ae05fc"
homepage: "https://github.com/fleetcaptain/dtp-spoof"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "fleetcaptain/dtp-spoof"
github-stars: 9
github-pushed: "2022-04-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dtp-spoof

> fleetcaptain's Scapy script that forges Cisco Dynamic Trunking Protocol frames to talk a negotiating switchport into trunk mode.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Testing the Dynamic Trunking Protocol (DTP) configuration of a switch. If the target port is left to negotiate its mode (dynamic auto / dynamic desirable), the script sends a forged DTP frame requesting trunking, which can move the port to trunk mode and expose VLANs beyond the attacker's own — the classic VLAN-hopping foothold. It sends one style of DTP packet per run (trunk by default, or dynamic desirable / dynamic auto / access) and optionally spoofs the source MAC. It achieves nothing against ports with DTP disabled or hard-set to access/trunk mode, and the switch sends no confirmation of success — verify separately by sniffing DTP/CDP or by sending 802.1Q-tagged traffic for other VLANs. Python 2 + Scapy; use only on networks you own or are authorized to audit.

## Typical use cases

### Ask a negotiating port to become a trunk

```
dtp-spoof -i eth0
```

Outcome: transmits a DTP Trunk packet out eth0 using the interface's real MAC as both the Ethernet frame source and the DTP neighbor MAC; nothing is printed or echoed back, so afterwards check whether tagged traffic for other VLANs now crosses the port.

### Spoof the source MAC in the DTP request

```
dtp-spoof -i eth0 -m 00:05:3a:55:3a:1c
```

Outcome: sends the same trunk request with the supplied MAC as both the frame source address and the neighbor MAC inside the DTP packet, hiding the transmitter's real hardware address from the switch's point of view.

### Send a different negotiation mode

```
dtp-spoof -i eth0 --desirable
```

Outcome: emits a Dynamic Desirable DTP packet instead of the default Trunk request; `--auto` and `--access` likewise send dynamic-auto and plain access (non-trunking) requests, useful for probing which negotiation modes the port actually honors.
