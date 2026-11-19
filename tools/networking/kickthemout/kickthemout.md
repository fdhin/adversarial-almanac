---
name: "kickthemout"
categories:
  - blackarch-networking
version: "212.861aea2"
homepage: "https://github.com/k4m4/kickthemout"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "k4m4/kickthemout"
github-stars: 2685
github-pushed: "2023-02-27"
github-archived: false
github-issues: 120
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kickthemout

> Kicks devices off the LAN by ARP-spoofing them — continuously poisoning the victims' ARP resolution so they lose connectivity — from a menu-driven CLI or one-shot flags.

## What it's for

KickThemOut (by Nikolaos Kamarinakis and David Schütz) disconnects other devices from the local network by performing an ARP spoof attack against them: the targets' ARP state is poisoned so that traffic stops flowing and they drop off the LAN, leaving the bandwidth to you. Reach for it only on networks you administer — testing endpoint resilience to trivial ARP-spoof DoS, or booting devices off a lab LAN. It is a disruption tool, not a sniffer: no traffic is captured or relayed. Linux/macOS only, needs root, Python 3 with scapy-family packet support and nmap.

## Typical use cases

### Survey the LAN first

See who is online before deciding.

```
sudo kickthemout -s
```

Outcome: performs a quick network scan of the local subnet, prints the live hosts it finds, and exits without attacking anything.

### Kick specific targets

Boot chosen IPs and control the rate.

```
sudo kickthemout -t 198.51.100.10,198.51.100.20 -p 30
```

Outcome: ARP-spoofs exactly those two hosts, broadcasting 30 spoofed packets per minute (default 6) until interrupted; the targets lose network connectivity.

### Interactive mode

Pick specific or all victims from a menu.

```
sudo kickthemout
```

Outcome: opens the interactive menu offering a network scan and kicking either a selected device or every device off the LAN, with the same ARP-spoof mechanics driven by on-screen choices.

*No corpus evidence — derived from upstream documentation.*
