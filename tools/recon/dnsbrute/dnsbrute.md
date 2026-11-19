---
name: "dnsbrute"
categories:
  - blackarch-recon
version: "2.b1dc84a"
homepage: "https://github.com/d4rkcat/dnsbrute"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "d4rkcat/dnsbrute"
github-stars: 13
github-pushed: "2014-05-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnsbrute

> Minimal multi-threaded DNS subdomain bruteforcer.

## What it's for

dnsbrute does exactly one job with no ceremony: given a domain and a
wordlist, it resolves `word.domain` for every line and reports which
subdomains exist, spreading the lookups over threads (measured upstream at
roughly 80 lookups/second with 40 threads). There is no wildcard handling,
recursion or output-format machinery — when you want a one-liner dictionary
sweep against a resolver and nothing else, this is the tool; when you need
zone-transfer attempts, scraping or reverse lookups, reach for a fuller
enumerator. It is a small Python 2/dnspython script from 2014 that has not
changed since, which also means its behavior is completely predictable.

## Typical use cases

### Bruteforce subdomains from a wordlist

The default sweep.

```
dnsbrute example.com subdomains.txt
```

Outcome: resolves every wordlist entry as a subdomain of example.com using
the system resolver and prints each hostname that answers — a fast first-pass
inventory to feed into further enumeration.

### Raise the thread count for faster sweeps

Trade politeness for speed on a link that can take it.

```
dnsbrute example.com subdomains.txt 100
```

Outcome: same dictionary sweep with the optional third argument lifting
resolver threads to 100 (the default sits around 40); throughput scales with
threads until the resolver or upstream starts dropping queries, so watch the
output rate rather than assuming more is better.

No corpus evidence — derived from upstream documentation.
