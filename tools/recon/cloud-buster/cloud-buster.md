---
name: "cloud-buster"
categories:
  - blackarch-recon
version: "194.b55e4a1"
homepage: "https://github.com/SageHack/cloud-buster"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "SageHack/cloud-buster"
github-stars: 145
github-pushed: "2019-07-01"
github-archived: true
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cloud-buster

> Multi-technique Cloudflare origin-IP hunter: misconfiguration scans, historical databases and subdomain sweeps.

## What it's for

cloud-buster checks sites behind Cloudflare for origin IP leaks by combining independent techniques — MX records, the Crimeflare database, DNSDumpster and subdomain scanning — under an engine that certifies candidate matches before reporting them, and details both origin and related-service IPs. Scans are composed with `--scan`, subdomain coverage runs from a quick default pass to the 20,000 most popular subdomain names (`--dept full`), and a target file drives bulk runs. The upstream was archived when the author left the scene; the tool still runs, though its third-party data sources have aged.

## Typical use cases

### Fast scan of one domain

Run the quick default check for origin leaks.

```
cloud-buster example.com
```

Outcome: a fast default scan over the domain, printing any origin or related-service IPs the engine can certify — the fastest way to check whether a site leaks at all.

### Comprehensive scan

Combine every technique at normal depth.

```
cloud-buster example.com --scan mx crimeflare dnsdumpster subdomain --dept normal
```

Outcome: runs the full technique set — MX records, Crimeflare, DNSDumpster and subdomain resolution — with more subdomain coverage than the fast scan; slower, but cross-checks every source before certifying a match.

### Probe specific subdomain labels

Check exactly the labels most likely to bypass the CDN.

```
cloud-buster example.com --scan subdomain --sub www www2 ftp direct
```

Outcome: resolves only the named labels (www.example.com, direct.example.com, ...) and reports any that answer outside Cloudflare's network.

### Sweep a target list

Process a whole engagement's domains in one run.

```
cloud-buster domainlist.txt
```

Outcome: reads one domain per line and runs the same scan options against each, collecting certified origin leaks across the whole list.

*No corpus evidence — derived from upstream documentation.*
