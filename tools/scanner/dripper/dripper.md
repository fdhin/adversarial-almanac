---
name: "dripper"
categories:
  - blackarch-scanner
version: "v1.r1.gc9bb0c9"
homepage: "http://www.blackhatlibrary.net/Dripper"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-16"
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "BlackArch/dripper"
github-stars: 4
github-pushed: "2013-11-16"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dripper

> Stateless single-binary DNS brute-forcer that fires thousands of subdomain guesses (or reverse-DNS queries across a CIDR) and collects answers with a sniffer instead of waiting on replies.

*No corpus evidence — derived from upstream documentation.*

## What it's for

High-speed DNS enumeration in C by jtripper (2013). Instead of the send-request/wait-for-reply loop of naive brute-forcers, dripper sends queries statelessly and runs a packet sniffer alongside to pick up the answers as they arrive, keeping the resolver pipeline full and making large candidate lists practical. It has two modes: forward brute-forcing of subdomain names against a target domain from a wordlist, and reverse scanning of an IP range for PTR records (a domain is still required to scope the run). The wordlist (`subs.txt` by default) and a `resolv.conf`-format nameserver list (`resolv.conf` by default) are read from the working directory — both plain text files you curate yourself. The original upstream is gone; the packaged source is maintained in the BlackArch repository mirror.

## Typical use cases

### Brute-force subdomains of a domain

```
dripper -d example.com -s subs.txt
```

Outcome: sends a query for every name in the wordlist under the target domain and prints each hostname that resolves as the sniffer picks up the replies; misses produce no output and cost nothing but bandwidth.

### Enumerate hosts in an IP range by reverse DNS

```
dripper -d example.com -i 198.51.100.0/24
```

Outcome: sweeps the given CIDR with PTR queries instead of guessing names, surfacing named hosts inside the range; the `-d` domain remains a required argument to scope the search.

### Point it at specific resolvers

```
dripper -d example.com -r resolv.conf
```

Outcome: reads the nameserver list from the named file (standard `nameserver` entries, one per line) rather than the default file in the working directory — the way to spread query load across several resolvers of your choosing.
