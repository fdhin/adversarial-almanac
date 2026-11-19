---
name: "cloudfail"
categories:
  - blackarch-recon
version: "86.c84219f"
homepage: "https://github.com/m0rtem/CloudFail"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "m0rtem/CloudFail"
github-stars: 2694
github-pushed: "2024-03-26"
github-archived: false
github-issues: 60
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cloudfail

> Hunt the origin IP behind Cloudflare via misconfigured DNS, the Crimeflare database and a 2500-subdomain brute force.

## What it's for

CloudFail is a Python 3 reconnaissance tool for targets behind Cloudflare. It runs three phases: a misconfigured-DNS scan using DNSDumpster, a lookup in the Crimeflare database of domains whose origin was previously exposed, and a brute-force sweep over 2500 common subdomains looking for records that bypass the CDN. All requests can be routed through Tor to hide the source. It is explicitly a proof of concept — a properly configured Cloudflare site leaks nothing — and its third-party data sources have aged, so treat any finding as a lead to confirm manually.

## Typical use cases

### Scan a target for its origin

Run the three attack phases against one domain.

```
cloudfail --target example.com
```

Outcome: walks the DNSDumpster scan, Crimeflare lookup and subdomain brute force in sequence, printing any IP addresses that look like the site's true origin behind Cloudflare.

### Scan through Tor

Keep your source address out of the target's logs.

```
cloudfail --target example.com --tor
```

Outcome: proxies every request of the same scan through a running Tor service (start it first, e.g. `service tor start`), so DNSDumpster and subdomain probes do not originate from your address.

*No corpus evidence — derived from upstream documentation.*
