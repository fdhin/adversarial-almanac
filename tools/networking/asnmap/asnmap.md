---
name: "asnmap"
categories:
  - blackarch-networking
version: "v1.1.1.r8.gb227b1b"
homepage: "https://github.com/projectdiscovery/asnmap"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/asnmap"
github-stars: 1148
github-pushed: "2026-11-02"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 139
gh-activity-checked: "2026-11-09"
---

# asnmap

> Resolve the CIDR network ranges behind an ASN, IP, domain or organization name.

## What it's for

A projectdiscovery Go CLI that turns "an organization on the Internet" into a list of routable CIDR blocks — the reconnaissance step that produces scannable IP ranges. It accepts AS numbers, IPs, domains and organization names via flags, files or stdin, prints ranges as plain text by default, and emits JSON or CSV with ASN metadata for automation. Current releases query the ASNMap API and need a free ProjectDiscovery Cloud Platform key, configured once with `asnmap -auth` or the `PDCP_API_KEY` environment variable.

## Typical use cases

### Enumerate the CIDRs announced by an AS number

Turn an autonomous system number into scannable ranges.

```
asnmap -a AS14421 -silent
```

Outcome: prints each IPv4 CIDR block announced by that AS, one per line; add `-v6` to include IPv6 ranges in the output.

### Map a domain or IP back to its network ranges

Find which CIDRs sit behind a host.

```
asnmap -d example.com -silent
asnmap -i 93.184.216.34 -silent
```

Outcome: resolves the domain's (or IP's) autonomous system and prints every CIDR range belonging to it.

### Enumerate an organization's footprint

Get all ranges registered to a company.

```
echo GOOGLE | asnmap -silent
```

Outcome: asnmap reads organization names from stdin (stdin accepts all supported input forms — ASN, IP, DNS, ORG) and prints every CIDR registered to that organization.

### Emit structured output for post-processing

Keep ASN metadata alongside the ranges.

```
asnmap -d example.com -json -silent
```

Outcome: prints one JSON object per result with `input`, `as_number`, `as_name`, `as_country` and `as_range` fields; `-csv` returns the same fields as pipe-separated columns.

### Feed the ranges straight into other tools

Chain range discovery with scanning.

```
echo AS54115 | asnmap -silent | naabu -p 443 | httpx
```

Outcome: each CIDR's addresses flow into the port scanner and then the HTTP prober, so one command goes from ASN to live HTTP services; use `-f targets.txt` to read inputs from a file instead of stdin.

*No corpus evidence — derived from upstream documentation.*
