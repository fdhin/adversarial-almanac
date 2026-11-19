---
name: "dnsvalidator"
categories:
  - blackarch-networking
version: "90.46f0f32"
homepage: "https://github.com/vortexau/dnsvalidator"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-09-28"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "vortexau/dnsvalidator"
github-stars: 751
github-pushed: "2026-09-14"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# dnsvalidator

> Filters lists of IPv4 DNS resolvers down to the ones that answer correctly.

## What it's for

dnsvalidator (vortexau) takes candidate DNS servers — from a file, a URL, or stdin — and validates each in two stages before you trust it: non-geolocated domains must resolve to the same IP as the trusted baselines 1.1.1.1, 8.8.8.8 and 9.9.9.9 (so spoofing or filtering resolvers drop out), and known commonly-spoofed names must return NXDOMAIN (so wildcard/hijacking resolvers drop out). Survivors can be written to a file for use as a resolver pool elsewhere. Two caveats from the author: the baseline root domain must not be geolocated or only locally-correct resolvers will pass, and keep -threads at a reasonable level — aggressive thread counts can look like an attack on DNS servers and earn network-level blocks from your ISP.

## Typical use cases

### Validate a public list of resolvers

```
dnsvalidator -tL https://publicdns.info/nameservers.txt -threads 20 -o resolvers.txt
```

Outcome: each resolver from the list is baseline-compared and NXDOMAIN-checked across 20 threads; servers failing a stage are skipped, and the verified ones are written to resolvers.txt, ready to feed other tooling.

### Check a single resolver verbosely

```
dnsvalidator -t 198.51.100.10 -r example.com -v
```

Outcome: verifies just that one server, comparing its answers for the non-geolocated root domain given with -r against the trusted baselines; -v prints each validation step so you can see exactly which stage a resolver fails.

### Pipe candidates from another tool

```
cat candidates.txt | dnsvalidator --silent -o validated.txt
```

Outcome: reads the candidate servers from stdin, strips banners and colors (--silent) so only successfully validated servers are displayed, and writes them to validated.txt — resolver enumeration can be piped straight into validation.

*No corpus evidence — derived from upstream documentation.*
