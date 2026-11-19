---
name: "allthevhosts"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "http://labs.portcullis.co.uk/tools/finding-all-the-vhosts/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "CiscoCXSecurity/allthevhosts"
github-stars: 2
github-pushed: "2021-04-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# allthevhosts

> Python 2 web scraper that maps every website hosted on an IP address by aggregating reverse-lookup web services and certificate data.

## What it's for

Given an IP address or URL, allthevhosts collects every hostname claimed to share that
address. It queries Bing's `IP:` search, my-ip-neighbors.com, ipneighbour.com and
yougetsignal.com, plus certificate Subject Alternative Names, then resolves each candidate
itself and keeps only names that genuinely resolve back to the target address — search
service results are verified, not trusted. This makes it a quick first pass for virtual
host discovery when you know a web server but not the sites it hosts. It is a Python
2-era Portcullis tool (mirrored on GitHub by Cisco CX Security), so expect breakage as the
third-party lookup sites it depends on change or disappear. It does not brute-force
hostnames; it only aggregates and verifies what public services already know.

## Typical use cases

### Enumerate the vhosts on a web server

Discover which sites live on a known address.

```
allthevhosts 198.51.100.10
```

Outcome: queries Bing `IP:`, the neighbor-lookup services and certificate SANs, then
DNS-resolves every candidate; prints each hostname that resolves to the target IP, one per
line, with progress messages per source.

### Save the vhost list to a file

Keep the verified hostnames for later fuzzing.

```
allthevhosts target.example.com vhosts.txt
```

Outcome: same enumeration against a hostname target, but writes the verified vhost list to
`vhosts.txt` instead of stdout — ready to feed ffuf, gobuster or a probing loop.

*No corpus evidence — derived from upstream source (Cisco CX Security mirror of the Portcullis original; invocation from the script header).*
