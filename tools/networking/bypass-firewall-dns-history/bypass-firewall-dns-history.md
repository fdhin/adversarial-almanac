---
name: "bypass-firewall-dns-history"
categories:
  - blackarch-networking
version: "33.c55b7ce"
homepage: "https://github.com/vincentcox/bypass-firewalls-by-DNS-history"
packaged-by: [blackarch]
blackarch-builddate: "2020-10-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "vincentcox/bypass-firewalls-by-DNS-history"
github-stars: 1311
github-pushed: "2022-09-05"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bypass-firewall-dns-history

> Find the origin server IP hidden behind a WAF by mining historical DNS records.

## What it's for

A Bash script (by Vincent Cox) for web assessments: when a site sits behind a filtering proxy such as Cloudflare, Incapsula or Sucuri, it queries historical-DNS and OSINT services (SecurityTrails, ViewDNS, DNSDumpster, certspotter, IPinfo, CrimeFlare) for old A records and subdomain IPs, tests which of those addresses still serve the site, and scores each candidate origin with a confidence level based on HTML similarity between the candidate response and the WAF-fronted one. The found origin lets a tester send traffic straight to the backend (hosts-file entry or Burp override), bypassing WAF filtering — equally useful to defenders hunting forgotten, unprotected origin servers. Requires bash; `jq` is optional and only enables automatic subdomain gathering.

## Typical use cases

### Hunt the origin IP behind a firewall

Basic run against a WAF-fronted domain.

```
bypass-firewall-dns-history -d example.com
```

Outcome: prints every candidate origin IP found via DNS-history and subdomain lookups, each with a confidence score derived from the HTML match with the live site — the highest-confidence IP is the likely true origin.

### Save the results

Keep the candidates for the report.

```
bypass-firewall-dns-history -d example.com -o origins.txt
```

Outcome: writes the discovered IP addresses to `origins.txt` in addition to the console output.

### Widen the search with known subdomains

Feed your own subdomain enumeration in.

```
bypass-firewall-dns-history -d example.com -l subdomains.txt -a
```

Outcome: uses your subdomain list (one hostname per line) for extra IP coverage, and `-a` additionally checks every subdomain for a WAF bypass — slower, but catches origins exposed only via secondary hostnames.

*No corpus evidence — derived from upstream documentation.*
