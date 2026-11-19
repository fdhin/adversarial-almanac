---
name: "above"
categories:
  - blackarch-sniffer
version: "v2.8.1.r0.g313f550"
homepage: "https://github.com/casterbyte/Above"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-01-15"
build-system: python
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "casterbyte/Above"
github-stars: 877
github-pushed: "2026-01-02"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# above

> Passive network sniffer that turns traffic from discovery, routing and ICS protocols into concrete attack vectors and mitigation notes.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Above (casterbyte, Scapy-based) listens on a segment — or on a saved capture — and detects 28 protocols that leak infrastructure detail: CDP, LLDP, DTP, VLAN 802.1Q, EAPOL/MACSec, ARP, STP, FHRP (HSRP/VRRP/GLBP), OSPF/EIGRP/BGP, LLMNR/NBT-NS/mDNS/DHCP/DHCPv6/ICMPv6, SSDP, MNDP, TACACS+, SNMP, and SCADA/ICS stacks (ModbusTCP, S7COMM, OMRON). For every detection it prints the attack impact, tools that can exploit it, the technical detail (addresses, FHRP group IDs, OSPF/EIGRP domains, port IDs) and a mitigation — i.e. it triages network-equipment attack surface. It is entirely passive (sends nothing) and works in two modes: live on an interface with a timer, or offline on a `.pcap`. It is not an active scanner or an exploit tool itself.

## Typical use cases

### Live-sniff a segment for a fixed window

```
sudo above --interface eth0 --timer 300
```

Outcome: listens passively on eth0 for 5 minutes; every recognized protocol event prints impact, suggested attack tools, technical data with source/destination MAC and IP addresses, and a mitigation. Nothing is transmitted, so the operator stays invisible to the segment.

### Triage an existing capture offline

```
above --input capture.pcap
```

Outcome: runs the same 28-protocol detectors over a saved pcap file — no interface or elevated privileges needed — producing the same vector/mitigation report, useful when auditing captures taken elsewhere.

### Map hosts and VLANs while archiving traffic

```
sudo above --interface eth0 --passive-arp --search-vlan --output segment.pcap
```

Outcome: additionally builds a host inventory from ARP traffic (`--passive-arp`), extracts VLAN IDs from 802.1Q-tagged frames (`--search-vlan`), and writes everything heard to `segment.pcap` (`--output`) for later offline analysis.
