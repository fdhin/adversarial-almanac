---
name: "fierce"
categories:
  - blackarch-recon
version: "145.965840d"
homepage: "https://github.com/mschwager/fierce"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mschwager/fierce"
github-stars: 1828
github-pushed: "2024-08-28"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fierce

> DNS reconnaissance for locating non-contiguous IP space: recursive subdomain discovery, nearby-IP traversal, wildcard filtering and zone-transfer attempts.

## What it's for

fierce (originally by RSnake at ha.ckers.org; this is the maintained Python 3 conversion by mschwager) is a semi-lightweight DNS scanner meant as a pre-cursor to nmap and friends: it locates non-contiguous IP space and hostnames against a specified domain, using DNS queries — recursion against the domain's nameservers, subdomain guessing, nearby-IP traversal to find contiguous blocks, and AXFR zone-transfer attempts. It filters wildcard DNS responses so brute-force results stay meaningful, does not perform exploitation, and does not scan the whole internet indiscriminately. Reach for it in the first minutes of external recon to map what IP space and hostnames actually belong to the target; misconfigured networks frequently leak internal hostnames this way.

## Typical use cases

### Basic enumeration with a subdomain list

Find which of your guesses resolve.

```
fierce --domain example.com --subdomains accounts admin ads
```

Outcome: queries the domain's authoritative nameservers for each listed subdomain, printing every hit with its IP addresses and nearby DNS relationships — the resolved hostnames are your scan candidate list.

### Traverse IPs near discovered hosts

Hunt for contiguous blocks around hits.

```
fierce --domain example.com --subdomains admin --traverse 10
```

Outcome: after each discovered subdomain, walks up to 10 nearby IPs on its class C block looking for more DNS names, surfacing hosts that share the block; use `--search example.com example.net` to restrict traversal results to given domains.

### Wide /24 sweep

Exchange speed for breadth.

```
fierce --domain example.com --wide
```

Outcome: expands traversal to the entire /24 of every discovered domain, mapping all nearby names at the cost of many more queries — use when thoroughness matters more than speed. Add `--connect` to attempt an HTTP connection to each discovered domain.

### Attempt a zone transfer

Ask the nameservers to hand over the zone.

```
fierce --domain zonetransfer.me
```

Outcome: tries an AXFR against each authoritative nameserver of the domain; on a misconfigured server the entire zone (all records) is printed — the DNS castle's keys. zonetransfer.me is a public service designed for testing this.

### Scan an internal range against internal resolvers

Enumerate reverse space on an internal network.

```
fierce --dns-servers 198.51.100.1 --range 198.51.100.0/24
```

Outcome: queries the specified internal DNS server across the whole /24 range, reporting every resolving hostname in the block; redirect output (`> output.txt`) to keep the results.

No corpus evidence — derived from upstream documentation.
