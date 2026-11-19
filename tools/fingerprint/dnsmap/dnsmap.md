---
name: "dnsmap"
categories:
  - blackarch-fingerprint
version: "51.d727cfb"
homepage: "https://github.com/pagvac/dnsmap"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "pagvac/dnsmap"
github-stars: 23
github-pushed: "2025-11-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# dnsmap

> Zero-options subdomain enumerator — scrapes public OSINT sources and then brute-forces DNS with a built-in wordlist, printing confirmed subdomains to stdout.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding subdomains of a domain during reconnaissance or bug-bounty enumeration with no flags to learn. dnsmap (by pagvac, the original author of the classic C dnsmap, rewritten in Python) first queries public scraping sources for known subdomains, then brute-forces DNS using a large internal wordlist, and emits only confirmed subdomains on stdout — progress bars and statistics go to stderr. The deliberate stdout/stderr split means output can be redirected straight into a file and piped into resolvers, HTTP fuzzers or further OSINT pipelines without any filtering. It is an active enumeration tool (it sends DNS queries and touches scraping sources), not a passive traffic analyzer; it intentionally ships without command-line options.

## Typical use cases

### Enumerate a domain's subdomains

Get the full subdomain picture for one domain.

```
dnsmap example.com
```

Outcome: prints every confirmed subdomain one per line on stdout (scraped finds plus brute-force hits), ending with a `[stats] duration=... attempted=... found=...` line on stderr summarizing the run.

### Save a clean list for further tooling

Capture just the results, machine-processed.

```
dnsmap example.com > subs.txt
```

Outcome: writes only the enumerated subdomains to subs.txt while progress and telemetry stay on stderr, so the file can be fed directly into resolvers, fuzzers or the next enumeration stage.
