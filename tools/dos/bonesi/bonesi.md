---
name: "bonesi"
categories:
  - blackarch-dos
version: "12.733c9e9"
homepage: "https://github.com/Markus-Go/bonesi"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-21"
build-system: autotools
license: Apache2
meta-checked: "2026-11-09"
github-repo: "Markus-Go/bonesi"
github-stars: 783
github-pushed: "2026-07-27"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# bonesi

> BoNeSi, the DDoS botnet simulator — spoofed ICMP and UDP floods plus full spoofed-TCP HTTP floods from thousands of fake bot addresses.

## What it's for

BoNeSi ("Botnet Simulator") generates realistic distributed-denial-of-service traffic in a testbed: ICMP and UDP floods, and — its distinguishing capability — TCP HTTP-GET floods whose source IP addresses are spoofed. For spoofed TCP it includes a small stack that sniffs the network interface and answers the server's SYN-ACKs, which only works when response traffic is routed back to the host running BoNeSi, so it is designed for closed lab networks. Rates, payload sizes, botnet size (via IP address lists), requested URLs and browser user agents are all configurable, and it deliberately avoids packet patterns that are easy to filter. Built at DFKI (and maintained by Markus Goldstein) to study DDoS effects and exercise mitigation systems, it reaches hundreds of thousands of packets per second on commodity hardware.

### Simulate a UDP flood from a botnet IP list

Send spoofed UDP traffic sized and paced like a real botnet.

```
sudo bonesi -i 50k-bots -p udp -r 1000 -c 50000 198.51.100.10:8080
```

Outcome: fires up to 50,000 UDP packets capped at 1,000 packets per second at the target, each with a source address drawn from the 50,000-entry bot list shipped with the tool; interval counters accumulate in the statistics file (`stats` by default). Requires root.

### Simulate a spoofed-source HTTP-GET flood

The capability that separates BoNeSi from plain packet generators: complete TCP handshakes from fake addresses.

```
sudo bonesi -i 50k-bots -p tcp -d eth0 -u /search -l urllist.txt -b browserlist.txt 198.51.100.10:80
```

Outcome: opens TCP connections with spoofed bot addresses (listening for replies on eth0) and issues HTTP GETs over randomly chosen URLs from `urllist.txt` with user agents rotated from `browserlist.txt`. Success is visible in the web server's access log as simultaneous requests from many distinct addresses, all fetching different URLs.

### Simulate an ICMP flood with fixed payload size

Bandwidth-oriented ICMP pressure to test rate limiting.

```
sudo bonesi -i 50k-bots -p icmp -s 512 -r 500 -o icmp_stats 198.51.100.10:0
```

Outcome: sends 512-byte ICMP packets from spoofed bot addresses at 500 packets per second, with statistics written to `icmp_stats` — use the target's ICMP rate-limit counters to see where it starts discarding.

*No corpus evidence — derived from upstream documentation.*
