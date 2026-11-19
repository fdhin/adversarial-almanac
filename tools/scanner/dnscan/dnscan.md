---
name: "dnscan"
categories:
  - blackarch-scanner
version: "209.281721e"
homepage: "https://github.com/rbsec/dnscan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "rbsec/dnscan"
github-stars: 1281
github-pushed: "2024-12-17"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnscan

> Wordlist-based DNS subdomain scanner that tries zone transfer first, then falls back to recursive brute-forcing.

## What it's for

dnscan (rbsec) enumerates a domain's subdomains with a staged approach:
first a zone transfer (AXFR) attempt against each of the domain's
nameservers, then TXT/MX record lookups, then a wordlist-based recursive
scan using the bundled `subdomains*.txt` lists (built from analysis of
~86,000 transferred zone files, sorted by subdomain popularity). It also
knows several tricks beyond plain brute force: alteration permutations
(dev/test/01 prefixes), TLD scanning against the IANA list, custom
insertion points, and per-scan resolver selection. It needs Python 3 with
dnspython and netaddr.

## Typical use cases

### Default subdomain scan

Enumerate a domain the standard way.

```
dnscan -d example.com
```

Outcome: tries a zone transfer via each authoritative nameserver, looks up
TXT/MX records, then brute-forces subdomains with the default bundled
`subdomains.txt` (~770 entries) — each hit is printed as
`sub.example.com - 198.51.100.10` with the resolver that answered.

### Zone transfer only

Check quickly whether misconfigured NS leak the whole zone.

```
dnscan -d example.com -z
```

Outcome: attempts AXFR against every nameserver of the domain and exits —
a successful transfer dumps the entire zone (all records, not just
subdomains), which ends subdomain enumeration for that domain on the spot.

### Recursive scan with a bigger list and more threads

Go deeper on a large domain.

```
dnscan -d example.com -w /usr/share/dnscan/subdomains-1000.txt -r -m 2 -t 16
```

Outcome: brute-forces with the 1000-entry popularity list, recurses two
levels deep (`-r -m 2`) into every subdomain found (scanning
`sub.sub.example.com` too) using 16 threads; add `--recurse-wildcards`
to also recurse into wildcard answers (slow) or `-a` to additionally try
name alterations.

### Targeted patterns and custom resolvers

Probe specific naming schemes or avoid the local resolver.

```
dnscan -d dev-%.example.com -w prefixes.txt -R 198.51.100.53
```

Outcome: substitutes each wordlist entry for the `%` insertion point
(`dev-<word>.example.com`) and performs all lookups via the resolver at
198.51.100.53 (`-R`; `-L` reads a list of resolvers instead) — useful for
rate-limit avoidance and for patterns like `dev-`, `staging-` or
per-office prefixes.

### Save results to files

Keep the enumeration for later tooling.

```
dnscan -d example.com -o subs.txt -i ips.txt
```

Outcome: writes discovered subdomains to `subs.txt` and their IP addresses
to `ips.txt` (`-i`, useful as a direct input to masscan/nmap target lists),
in addition to the on-screen report.

*No corpus evidence — derived from upstream documentation (rbsec/dnscan
README, the packaged upstream).*
