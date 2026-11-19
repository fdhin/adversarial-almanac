---
name: "cyberscan"
categories:
  - blackarch-networking
version: "87.24c69b9"
homepage: "https://github.com/medbenali/CyberScan"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "medbenali/CyberScan"
github-stars: 472
github-pushed: "2026-04-23"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 12
gh-activity-checked: "2026-11-09"
---

# cyberscan

> Scapy-based multi-tool: host discovery by ARP/ICMP/TCP/UDP ping, port scanning, IP geolocation, and pcap header decoding.

*No corpus evidence — derived from upstream documentation.*

## What it's for

CyberScan (by Mohamed Ben Ali, medbenali) folds routine network-recon tasks into one Python CLI built on scapy: ping sweeps over several protocols, port scanning (an explicit range or a built-in common-ports list), GeoIP lookup of an address, and offline decoding of Ethernet/IP/TCP/UDP/ICMP headers from a pcap file. Reach for it as a quick triage tool — one binary for discovery plus a light scan — rather than as a substitute for dedicated scanners: no service fingerprinting, no scripting engine, single host or range per flag. The upstream code targets Python 3; the packaged revision runs the older 2.x codebase with the same flags.

## Typical use cases

### Sweep a subnet with ARP ping

Discover live hosts on the local segment fast.

```
cyberscan -s 198.51.100.0/24 -p arp
```

Outcome: sends ARP requests to all 256 addresses and reports how many answered; the fastest host-discovery method on a local Ethernet network.

### Ping with TCP or UDP when ICMP is blocked

Find hosts that drop echo requests.

```
cyberscan -s 198.51.100.1-254 -p tcp -d 80
```

Outcome: sends TCP packets to port 80 across the range; live hosts answer (SYN/ACK or RST) where they would ignore ICMP. `-p udp` works the same way via ICMP port-unreachable errors from live hosts.

### Port-scan a host

Check a port range or the common-port list.

```
cyberscan -s 198.51.100.10 -p scan -d 1 -t 100
```

Outcome: scans ports 1 through 100 and prints the open ones with service labels (for example `23 TELNET: Open`, `80 HTTP: Open`). Omitting `-d`/`-t` scans a built-in list of the most common ports instead.

### Geolocate an IP address

Pull the GeoIP record for a host.

```
cyberscan -s 198.51.100.10 -p geoip
```

Outcome: prints the city, region, country, latitude/longitude, and timezone for the address from the local GeoIP database.

### Decode headers from a capture file

Inspect packet headers offline.

```
cyberscan -f capture.pcap -p ip
```

Outcome: walks the pcap and prints each packet's decoded IP header fields (addresses, TTL, protocol, checksum, options) plus a hex dump; `-p eth`, `-p tcp`, `-p udp`, and `-p icmp` select the other layers.
