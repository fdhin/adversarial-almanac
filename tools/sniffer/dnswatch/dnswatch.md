---
name: "dnswatch"
categories:
  - blackarch-sniffer
version: "dnswatch.1.r18.g7ee12c9"
homepage: "https://github.com/HalilDeniz/DNSWatch"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "HalilDeniz/DNSWatch"
github-stars: 216
github-pushed: "2025-02-15"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnswatch

> Scapy-based DNS traffic monitor that filters live lookups by domain, IP, port and record type, and flags spoofing.

## What it's for

DNSWatch (Halil Ibrahim Deniz) is a Python 3 / Scapy tool for watching and analyzing DNS activity on a network. It sniffs DNS packets on a chosen interface and can filter what it shows by source or destination IP, port, target domains and DNS record type, resolve names over DNS-over-HTTPS, save the raw matches to a pcap file, and summarize the distribution of record types. A firewall mode counts queries per domain inside a sliding window and alerts when a threshold is exceeded — the signature of cache-poisoning or spoofing attempts, where a victim suddenly receives many unexpected responses. It is a monitoring and analysis aid, not a DNS server or interceptor: it observes traffic and alerts, it does not rewrite it.

## Typical use cases

### Watch all DNS traffic live

See every lookup crossing an interface.

```
sudo python3 dnswatch.py -i eth0 -v
```

Outcome: DNS queries and responses on eth0 are printed as they occur; verbose mode adds packet-level detail (endpoints, flags, full records) to the one-line summaries.

### Monitor specific domains only

Track lookups for a watchlist of names.

```
sudo python3 dnswatch.py -i eth0 -D example.com example.org
```

Outcome: only DNS traffic involving the listed domains is shown — useful for confirming which clients still resolve migrated or supposedly-retired names.

### Filter by record type

Isolate one kind of lookup.

```
sudo python3 dnswatch.py -i eth0 --dns-type 1
```

Outcome: only type-A (IPv4 address) queries are displayed; changing the numeric type (e.g. 28 for AAAA, 15 for MX) narrows the view to that record class. Adding `-d` produces an analysis of the DNS request types observed.

### Save matches to a pcap

Keep the filtered traffic for later analysis.

```
sudo python3 dnswatch.py -i eth0 -s 198.51.100.10 --pcap-file captured.pcap
```

Outcome: packets from source 198.51.100.10 are matched live as above and additionally written to `captured.pcap`, openable in Wireshark or tcpdump; stop the run with Ctrl-C.

### Detect DNS spoofing bursts

Alert when answer volume exceeds a normal rate.

```
sudo python3 dnswatch.py -i eth0 --firewall --threshold 50 --window-size 60
```

Outcome: firewall mode counts DNS queries per domain within a 60-second sliding window and raises an alert whenever a domain exceeds 50 — a pattern typical of spoofing or poisoning attempts rather than normal client behavior.

*No corpus evidence — derived from upstream documentation.*
