---
name: "habu"
categories:
  - blackarch-scanner
version: "0.1.6.r171.g9868861"
homepage: "https://github.com/fportantier/habu"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "fportantier/habu"
github-stars: 994
github-pushed: "2025-12-30"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 12
gh-activity-checked: "2026-11-09"
---

# habu

> Fabian Portantier's Python network-hacking toolkit: dozens of small composable `habu.<name>` commands for ARP, DHCP, DNS, OSINT and utility chores.

## What it's for

habu implements classic network attack and recon techniques as independent small commands —
each a separate console entry point built on click and scapy: ARP ping/poison/sniff, DHCP
discover and starvation, DNS lookups, subdomain and vhost discovery, certificate cloning,
WHOIS/ASN/geolocation lookups, web-technology fingerprinting, breach and social username checks,
plus utilities (hashing, XOR, config). Reach for it when you want one specific technique as a
one-liner instead of a framework; it is not a scanner suite or an exploitation platform. Most
network-attack commands need root and a specified interface.

## Typical use cases

### Host discovery on the local segment

Check liveness with ARP instead of ICMP.

```
sudo habu.arp.ping 198.51.100.10
```

Outcome: sends ARP requests to the address and prints a scapy packet summary (`Ether / ARP is-at
<mac> says <ip>`) for each reply; `-i` selects the interface when several exist.

### Man-in-the-middle two hosts

Poison both directions of a conversation.

```
sudo habu.arp.poison 198.51.100.1 198.51.100.77
```

Outcome: floods both victims with forged `is-at` replies pointing the pair at your machine,
printing each packet sent. To complete the relay, enable forwarding first
(`echo 1 > /proc/sys/net/ipv4/ip_forward`); stop with Ctrl-C.

### Watch the segment's ARP traffic

Passively map who is talking.

```
sudo habu.arp.sniff -i eth0
```

Outcome: prints a live table — seconds since last packet, IP, MAC and vendor — for every device
heard on the interface, building a picture of the local segment without sending anything.

### Enumerate DHCP servers

Discover who answers address requests.

```
sudo habu.dhcp.discover -v
```

Outcome: broadcasts a DHCP request and prints every responder as a scapy BOOTP/DHCP summary;
`-v` includes the offered options such as DNS servers, and `-t` bounds the wait window.

### Exhaust a DHCP server's leases

Starve the pool as the setup for a spoofing follow-up.

```
sudo habu.dhcp.starvation
```

Outcome: sends repeated DHCP requests from forged MAC addresses until the server's address pool
is exhausted and it stops answering — the state a rogue-DHCP attack needs.

*No corpus evidence — derived from upstream documentation (fportantier/habu README.rst command
reference; repo renamed from portantier/habu, license corrected from the repository's LICENSE).*
