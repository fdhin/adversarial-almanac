---
name: "voiphopper"
categories:
  - blackarch-automation
version: "2.04"
homepage: "http://voiphopper.sourceforge.net/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2015-09-06"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "iknowjason/voiphopper"
github-stars: 82
github-pushed: "2024-01-30"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# voiphopper

> VLAN-hops into the voice VLAN by mimicking an IP phone — via CDP, LLDP-MED spoofing, or vendor DHCP options (Avaya, Nortel, Alcatel).

## What it's for

VoIP Hopper (by Jason Ostrom) answers one question fast: can a PC plugged into a VoIP switchport get onto the voice VLAN? It mimics the behavior of IP phones to discover the 12-bit Voice VLAN ID (VVID) — by sniffing or spoofing Cisco Discovery Protocol, spoofing LLDP-MED, dissecting 802.1q-tagged ARP traffic, or sending vendor-specific DHCP requests (Avaya Option 176/242, Nortel Option 191, Alcatel Option 43). Once it knows the VVID it creates a virtual 802.1q-tagged interface, sends a tagged DHCP request, and lands an IP address inside the voice subnet — from where the actual VoIP infrastructure (phones, PBX) is reachable. Its interactive assessment mode also passively enumerates hosts on both the data and voice VLANs into `hosts.txt` / `voip-hosts.txt`. The project is no longer actively developed (the GitHub mirror is archived; Kali keeps the package), but the voice-VLAN misconfigurations it tests for are still common.

## Typical use cases

### Interactive assessment mode

```
sudo voiphopper -i eth0 -z
```

Outcome: opens the menu-driven assessment interface; a libpcap sniffer loop watches for CDP, LLDP-MED or 802.1q frames, automatically hops into the first voice VLAN discovered, adds an ARP sniffer on the new interface, and records discovered phones to `voip-hosts.txt` — the recommended first move against an unknown network.

### Hop via Cisco CDP

```
sudo voiphopper -i eth0 -c 0
sudo voiphopper -i eth0 -c 2
```

Outcome: `-c 0` passively sniffs CDP until the switch advertises the voice VLAN, then hops; `-c 2` spoofs a pre-built Cisco 7971G-GE CDP packet for the fastest hop (use `-c 1` with `-E/-P/-C/-L/-S/-U` to craft a custom CDP spoof instead).

### Hop into a known VLAN ID directly

```
sudo voiphopper -i eth0 -v 200
```

Outcome: skips discovery, creates the tagged interface for VLAN 200 and sends a tagged DHCP request — use when you already know the voice (or other) VLAN ID.

### Vendor-specific discovery: Avaya, Nortel, Alcatel, LLDP-MED

```
sudo voiphopper -i eth0 -a
sudo voiphopper -i eth0 -n
sudo voiphopper -i eth0 -t 0
sudo voiphopper -i eth0 -o 00:50:60:03:99:CB
```

Outcome: `-a` sends an Avaya-compliant DHCP request (Option 176/242) to learn the VVID, `-n` the Nortel equivalent (Option 191), `-t 0` an Alcatel Option-43 request (`-t 1` Alcatel LLDP-MED spoof, `-t 2` manual VLAN), and `-o` spoofs an LLDP-MED packet with the given phone MAC. Each hops as soon as the VLAN is learned; add `-m MAC` to spoof a phone MAC in the DHCP client.

### Clean up the created interface

```
sudo voiphopper -d eth0.200
```

Outcome: deletes the virtual tagged interface (e.g. `eth0.200`) created by an earlier hop, restoring the host to its normal data-VLAN state.

*No corpus evidence — derived from upstream documentation.*
