---
name: "asn"
categories:
  - blackarch-recon
version: "0.78.0"
homepage: "https://github.com/nitefood/asn"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-10-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "nitefood/asn"
github-stars: 1949
github-pushed: "2026-10-02"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 12
gh-activity-checked: "2026-11-09"
---

# asn

> One-command OSINT lookup for AS numbers, IPs and prefixes: routing data, RPKI validity, reputation and geolocation.

## What it's for

A bash CLI for network investigation: give it an AS number, IPv4/IPv6 address, prefix, hostname, URL or organization name and it returns the matching routing and ownership data — organization, RIR region, announced prefixes, BGP neighbours, peering relationships, AS rank, IP reputation and geolocation. By default it also runs an AS path trace (via mtr) towards IP targets, and with `-d` it validates each hop against RPKI ROAs and flags potential route leaks or BGP hijacks. It can additionally serve as a browser-driven lookup/traceroute web server (`-l`) and emit JSON for scripting. It is purely passive — it queries public APIs, never the target.

## Typical use cases

### Look up an IP or hostname with its AS path

```
asn 198.51.100.10
```

Outcome: prints the IP's prefix, originating ASN and organization, reputation/geolocation data, and an AS path trace from your host to the target, naming every AS along the way.

### Detailed trace with RPKI validation

```
asn -d 198.51.100.10
```

Outcome: same trace, but each hop gains organization/prefix detail and an RPKI ROA validity check; a failed validation prints a warning naming the AS that is wrongly announcing the hop prefix — a route-leak or hijack indicator.

### Look up an AS number without tracing

```
asn -n AS15169
```

Outcome: prints the AS's organization, RIR region, AS rank, BGP statistics (neighbours, v4/v6 prefix counts), peering relationships grouped by type and its announced prefixes — no path trace.

### Map an organization's network ranges

```
asn -o "Example Corp"
```

Outcome: forces an organization-name search and lists the IPv4/IPv6 ranges exported by or related to that company; if several organizations match, a selection menu is presented.

### Find ASNs by name

```
asn -a google
```

Outcome: lists every ASN whose name matches the search string, each with its AS rank and tags highlighting the highest-ranking results — a quick way to map one company's AS footprint.

### Passive recon of a target range via Shodan

```
asn -s 198.51.100.0/24
```

Outcome: queries Shodan's InternetDB API for the range (also accepts IPs, hostnames and URLs, several at once) and reports open ports, known vulnerabilities, and software/hardware fingerprints without sending a packet to the target.

### Machine-readable output

```
asn -j 198.51.100.10
```

Outcome: emits compact JSON (use `-J` for pretty-printed) suitable for piping into `jq` or storing in a report pipeline.

*No corpus evidence — derived from upstream documentation.*
