---
name: "subdomainer"
categories:
  - blackarch-recon
version: "1.2"
homepage: "http://www.edge-security.com/subdomainer.php"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# subdomainer

> Python 2-era subdomain harvester that mines search engines and PGP keyserver indexes for hostnames under a target domain.

## What it's for

One of the earliest public subdomain enumeration tools, written by Christian Martorella (Edge-Security Research, also author of theHarvester). It scrapes search-engine result pages (Google, Yahoo, MSN) and PGP keyserver indexes for any string ending in the target domain, deduplicates the hits, and resolves each hostname to an IP address. It is a passive, historical tool: it sends no packets at the target's infrastructure, it only queries third-party sources. The packaged script is a Python 2 program targeting 2008-era search endpoints, several of which no longer exist in that form — treat it as a legacy/educational utility and reach for a modern passive enumerator for current work.

## Typical use cases

### Harvest subdomains via a search engine

Mine search results for hostnames belonging to a domain.

```
subdomainer -d example.com -m google -l 200
```

Outcome: paginates through Google results mentioning the domain, prints each unique subdomain found together with its resolved IP address, and a final total count.

### Harvest via PGP keyservers

Pull hostnames from PGP keyserver indexes, which often carry internal mail hostnames in UIDs.

```
subdomainer -d example.com -m pgp
```

Outcome: queries the PGP keyserver index for entries referencing the domain and prints matching subdomains; the result-limit option is not needed for this source.

### Save the results as an HTML report

Collect long result sets into a file instead of the terminal.

```
subdomainer -d example.com -m yahoo -l 100 -o report.html
```

Outcome: writes an HTML table of subdomain and IP pairs to report.html.

*No corpus evidence — derived from upstream documentation (packaged source verified from the archived edge-security.com script; flag reference taken from its embedded usage).*
