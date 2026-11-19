---
name: "python-cymruwhois"
categories:
  - blackarch-networking
version: "1.6"
homepage: "https://pypi.org/project/cymruwhois/#files"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-13"
build-system: python
aliases: ["python2-cymruwhois"]
meta-checked: "2026-11-09"
github-repo: "JustinAzoff/python-cymruwhois"
github-stars: 43
github-pushed: "2016-11-08"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# python-cymruwhois

> Python client and CLI for Team Cymru's whois.cymru.com IP-to-ASN lookup service.

## What it's for

This package (cymruwhois, by Justin Azoff) wraps Team Cymru's IP intelligence whois service: given an address it returns the origin ASN, BGP prefix, country code and netblock owner. It ships a Python API (`cymruwhois.Client`) plus a `cymruwhois` command that decorates lists of IPs from files or stdin, which makes it a quick enrichment step when triaging logs. Lookups hit the public whois.cymru.com service over TCP 43 — fine for thousands of addresses, not for bulk telemetry; the README points heavy users at the author's local-lookup project `asnlookup` for autonomy and throughput.

## Typical use cases

### Enrich a file of IP addresses

Map the addresses in a text file (one per line) to their ASN and owner.

```
cymruwhois -f asn,cc,owner ips.txt
```

Outcome: one line per input address showing the selected fields (asn, ip, prefix, cc, owner) as reported by whois.cymru.com; use `-d ','` to emit CSV instead of space-justified columns.

### Enrich addresses from a pipe

Decorate IPs produced by another command instead of from a file.

```
cat ips.txt | cymruwhois
```

Outcome: with no file arguments the CLI reads addresses from stdin and prints the full lookup — ASN, IP, BGP prefix, country and netblock owner — for each one.

### Look up from Python

Query the service programmatically.

```python
from cymruwhois import Client

c = Client()
r = c.lookup("198.51.100.10")
print(r.asn, r.owner)
```

Outcome: `r` carries `asn`, `owner`, `prefix` and `cc` for the address (e.g. `15169 GOOGLE - Google Inc.` for a Google address); `Client()` accepts host/port overrides if you run your own instance of the service.

*No corpus evidence — derived from upstream documentation.*
