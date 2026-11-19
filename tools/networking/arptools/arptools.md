---
name: "arptools"
categories:
  - blackarch-networking
version: "13.41cdb23"
homepage: "https://github.com/Lab-Zjt/ARPTools"
packaged-by: [blackarch]
blackarch-builddate: "2018-04-29"
build-system: cmake
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Lab-Zjt/ARPTools"
github-stars: 3
github-pushed: "2023-04-02"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# arptools

> Menu-driven console tool for ARP LAN discovery, ARP denial-of-service, victim traffic interception and DNS lookups.

## What it's for

A small C++ binary (built as `ARPtools`, installed as `arptools`) that bundles ARP-level operations on the local network into one interactive five-option menu: broadcast ARP requests to discover neighbours, attack a victim to cut it off the Internet, redirect a victim's traffic through the attacking host for packet capture, print the collected IP-MAC map, and resolve A/AAAA/CNAME records for a domain. It reads its own address, MAC and gateway from an `im.config` file in the working directory and must run as root. It is a hobby project with slow traffic forwarding and no command-line flags — everything happens through the menu.

## Typical use cases

### Discover live hosts on the local segment

Map the LAN before targeting it.

```
sudo arptools
```

At the menu choose `1` (ARP Broadcast), then `4` (Print IP-MAC Map).

Outcome: the tool broadcasts ARP requests built from the `im.config` settings (`ip=`, `mac=`, `gateway=` lines) and then prints every discovered neighbour as an IP-to-MAC table; hosts in that table are the candidates for the attack options.

### Cut a host off the Internet

Drop one LAN neighbour's connectivity.

```
sudo arptools
```

At the menu choose `2` (ARP Attack) and pick a victim from the list.

Outcome: the tool ARP-attacks the selected victim so that machine can no longer reach the Internet; pressing Enter stops the attack.

### Intercept a victim's traffic for sniffing

Redirect a neighbour's traffic through this host.

```
sudo arptools
```

At the menu choose `3` (Data Transmission) and pick a victim from the list.

Outcome: the victim's traffic is forwarded through this machine from then on, so a packet capture started here (e.g. Wireshark) shows the victim's activity; the redirection continues until the program is quit.

*No corpus evidence — derived from upstream documentation.*
