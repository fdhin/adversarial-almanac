---
name: "dnsdiag"
categories:
  - blackarch-networking
version: "v2.10.0.r2.g7bc7ac7"
homepage: "https://dnsdiag.org/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: python
meta-checked: "2026-11-09"
github-repo: "farrokhi/dnsdiag"
github-stars: 1073
github-pushed: "2026-11-05"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 90
gh-activity-checked: "2026-11-09"
---

# dnsdiag

> DNS measurement and troubleshooting suite: dnsping, dnstraceroute and dnseval.

## What it's for

dnsdiag (dnsdiag.org, by Babak Farrokhi) answers "is my DNS slow, hijacked, or broken?" with three tools: dnsping measures a resolver's latency, jitter and loss the way ping does for ICMP; dnstraceroute traces the network path DNS queries actually take so redirection or interception becomes visible; dnseval benchmarks a list of resolvers against each other so you can pick one on measured performance. All three speak modern transports — TCP, DoT, DoH, DoQ and HTTP/3 alongside UDP — and can surface DNSSEC status, EDNS flags, RFC 8914 extended errors, NSID, DNS cookies and EDNS Client Subnet. It measures and audits resolution; it does not enumerate or attack zones.

## Typical use cases

### Measure a resolver's latency and loss

```
dnsping -c 5 -s 8.8.8.8 example.com
```

Outcome: ping-style output — one line per query ("N bytes from 8.8.8.8: seq=1 time=X ms NOERROR") — followed by a statistics block (min/avg/max/stddev, requests transmitted vs responses received, percent lost) quantifying that resolver's responsiveness and jitter.

### Compare DNS transports with full response metadata

```
dnsping -c 5 --tls -t AAAA --dnssec --flags -s 8.8.8.8 example.com
```

Outcome: queries run over DNS-over-TLS (the header line shows proto: TLS) requesting DNSSEC validation; each reply is annotated with its flag word (e.g. [QR RD RA -- DO]) and any RFC 8914 extended DNS error code, showing how the resolver handles validation across transports. Swap --tls for --tcp, --doh, --quic or --http3 — the protocol options are mutually exclusive.

### Identify which anycast PoP answers you

```
dnsping -c 2 --nsid -s 8.8.8.8 example.com
```

Outcome: each response carries the resolver's Name Server Identifier (e.g. [NSID:gpdns-ams]), telling you which point of presence is actually handling your queries — useful for verifying geo-routing or anycast pinning. Similarly, --cookie surfaces RFC 7873 DNS cookies, and --ecs 203.0.113.0/24 shows how EDNS Client Subnet is echoed back.

### Trace the path DNS queries take

```
dnstraceroute --expert --asn -C -t A -s 8.8.4.4 example.com
```

Outcome: a traceroute of the DNS traffic toward the resolver listing hop IPs, their AS numbers and response times; --expert analyzes the hops and prints warnings about potential DNS traffic hijacking (e.g. inconsistent paths between runs). Also accepts --tcp, --quic or --http3 to trace modern transports.

### Benchmark a list of resolvers

```
dnseval -f public-servers.txt example.com
```

Outcome: sends the same query to every resolver listed in the file (one per line) and reports each server's response times side by side, letting you rank resolver candidates by measured latency and reliability. A public-servers.txt sample list ships in the source repository.

*No corpus evidence — derived from upstream documentation.*
