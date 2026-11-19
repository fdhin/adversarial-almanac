---
name: "xcname"
categories:
  - blackarch-scanner
version: "11.9c475a1"
homepage: "https://github.com/mandatoryprogrammer/xcname"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mandatoryprogrammer/xcname"
github-stars: 57
github-pushed: "2016-06-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xcname

> Finds expired-domain takeover candidates by checking whether CNAME records point at unregistered names.

## What it's for

A dangling CNAME — a subdomain still pointing at a domain nobody owns — is the raw material of subdomain takeover: register the expired target and you inherit the subdomain's traffic and authority. xcname (mandatoryprogrammer, 2016) automates the check: it reads a subdomain (or a whole list, or a zone transfer), resolves each CNAME, and asks the Gandi API whether the pointed-to domain is currently unregistered, i.e. buyable — which is why a Gandi API key is a required argument. It can also attempt AXFR against a domain and parse whatever records the transfer yields. It is a Python 2 recon tool for takeover hunting and DNS hygiene audits; it does not attempt the takeover itself, and availability verdicts depend on Gandi's API of the era.

## Typical use cases

### Check a single subdomain for a dangling CNAME

```
./xcname.py -g GANDI_API_KEY -d subdomain.example.com
```

Outcome: resolves the subdomain, follows its CNAME and reports whether the destination domain is expired/unregistered — a hit means the subdomain is a takeover candidate.

### Sweep a list of subdomains

```
./xcname.py -g GANDI_API_KEY -l domain_list.txt
```

Outcome: runs the same CNAME-plus-availability check for every entry in the file, printing which lines resolve to expired domains — the bulk form for recon pipelines.

### Pull records via zone transfer and audit them

```
./xcname.py -g GANDI_API_KEY -z example.com
```

Outcome: attempts an AXFR against `example.com`; if the server allows transfers, every CNAME in the zone is parsed and checked for expired destinations (`-l domain_list.txt -z` does the same across a list of zones).

### Enumerate a domain's nameservers

```
./xcname.py -g GANDI_API_KEY -d example.com -ns
```

Outcome: prints the nameservers authoritative for the domain — useful before the AXFR attempt, since a transfer only works against servers permitting it.

*No corpus evidence — derived from upstream documentation.*
