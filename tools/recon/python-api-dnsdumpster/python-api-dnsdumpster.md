---
name: "python-api-dnsdumpster"
categories:
  - blackarch-recon
version: "v0.11.r0.g7c60722"
homepage: "https://github.com/PaulSec/API-dnsdumpster.com"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-21"
build-system: python
license: custom:unknown
aliases: ["python2-api-dnsdumpster"]
meta-checked: "2026-11-09"
github-repo: "PaulSec/API-dnsdumpster.com"
github-stars: 284
github-pushed: "2026-03-25"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# python-api-dnsdumpster

> Unofficial Python client for dnsdumpster.com — one `search()` call returns a domain's DNS, MX, NS, TXT and host records plus the network-map image and XLS export.

## What it's for

This package (import name `dnsdumpster`) wraps the free dnsdumpster.com recon service: give it a domain and it returns structured DNS records — A/host entries with reverse DNS and ASN/provider, MX, NS, TXT — together with the base64-encoded network mapping image and the XLSX export data. It is a scraping wrapper, not an official API: use it responsibly within the site's terms, and expect breakage if the site changes. The primary interface is the `DNSDumpsterAPI` class from Python code; the packaged `api-dnsdumpster` launcher simply runs upstream's example script, which queries a hardcoded demo domain and ignores arguments — treat it as a smoke test, not a per-domain CLI.

## Typical use cases

### Look up a domain from Python

The library's core call.

```
python3 -c "from dnsdumpster.DNSDumpsterAPI import DNSDumpsterAPI; print(DNSDumpsterAPI(True).search('example.com'))"
```

Outcome: prints the result dictionary for the domain — `domain`, `dns_records` with `dns`/`mx`/`ns`/`txt`/`host` lists (each host entry carrying IP, reverse DNS, ASN, provider, country) — ready to iterate or dump to JSON.

### Save the network map image

Keep the DNSDumpster visualization artifact.

```
python3 -c "import base64; from dnsdumpster.DNSDumpsterAPI import DNSDumpsterAPI; r = DNSDumpsterAPI().search('example.com'); open('map.png','wb').write(base64.b64decode(r['image_data']))"
```

Outcome: writes the domain's network mapping diagram to `map.png` for the report; `r['xls_data']` carries the same export in XLSX form if you prefer the spreadsheet.

### Subdomain and mail inventory for a target

Turn the host records into an enumeration feed.

```
python3 -c "from dnsdumpster.DNSDumpsterAPI import DNSDumpsterAPI; r = DNSDumpsterAPI(True).search('example.com'); print('\n'.join(h['domain'] + ' ' + h['ip'] for h in r['dns_records']['host']))"
```

Outcome: prints one `hostname ip` line per discovered host record — a quick passive subdomain and mail-inventory list to feed into resolution or scanning tooling.

### Smoke-test the packaged launcher

Confirm the package works end to end.

```
api-dnsdumpster
```

Outcome: runs upstream's example script against its hardcoded demo domain and prints the DNS/MX/host/TXT sections plus whether the mapping image and XLS were retrieved — verifies the install and the service's current shape; it ignores any arguments passed.

No corpus evidence — derived from upstream documentation.
