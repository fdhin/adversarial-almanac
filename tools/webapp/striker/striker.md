---
name: "striker"
categories:
  - blackarch-webapp
version: "85.87c184d"
homepage: "https://github.com/UltimateHackers/Striker"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "UltimateHackers/Striker"
github-stars: 2352
github-pushed: "2023-06-04"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# striker

> One-argument recon and vulnerability scanning suite (s0md3v) for a whole domain.

## What it's for

Striker (UltimateHackers/s0md3v) is an offensive information and vulnerability scanner that
chains an entire recon workflow from a single argument: the domain. Given just a domain it
discovers subdomains from multiple sources, filters alive hosts, sweeps the most common
TCP ports, checks for misconfigured HTTP headers, `crossdomain.xml`, and sensitive files,
then crawls the subdomains to detect outdated JavaScript libraries, CMS, and technologies
in use. The vulnerability-scanning phase (phase 4) remained under development, so treat it
as a recon/misconfiguration tool. The current public code is the Striker 2.0 prototype, so
expect rough edges.

## Typical use cases

### Run the full recon workflow against a domain

One command, whole-domain attack surface discovery.

```
striker target.example.com
```

Outcome: enumerates subdomains from findsubdomains and SecurityTrails, probes alive hosts on common ports, sweeps for misconfigured HTTP response headers and sensitive files, and crawls discovered hosts to report outdated JS libraries and detected technologies — results printed per phase.

No corpus evidence — derived from upstream documentation.
