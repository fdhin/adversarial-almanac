---
name: "subscraper"
categories:
  - blackarch-recon
version: "34.29aa5cc"
homepage: "https://github.com/m8sec/subscraper"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "m8sec/subscraper"
github-stars: 979
github-pushed: "2024-06-19"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# subscraper

> Modular subdomain enumerator with built-in DNS resolution, HTTP probing and CNAME takeover checks.

## What it's for

SubScraper by m8sec (the repository moved from m8r0wn to m8sec, same author) enumerates subdomains through a set of interchangeable modules — certificate transparency (crt.sh, Certspotter), OSINT APIs (VirusTotal, Shodan, Censys, BeVigil, Chaos and others, several needing API keys), archives and DNS aggregators — and can layer DNS bruteforce on top. What sets it apart from plain listers is the enrichment it performs during enumeration: resolving each name to an IP, probing for live HTTP(S) services, and checking CNAMEs for dangling-delegation takeover opportunities. It accepts a single domain, a file of targets or STDIN, and writes text or CSV reports. Reach for it when enumeration and triage should happen in one pass.

## Typical use cases

### Enumerate and probe in one pass

Run the passive modules, resolve names and find live web services.

```
subscraper -d example.com -resolve -http -module-only
```

Outcome: all passive modules are queried, each discovered subdomain is resolved to an IP and probed on ports 80/443, with bruteforce skipped (-module-only); results print to the terminal with live hosts identified.

### Hunt subdomain takeovers

Check CNAME records of discovered hosts for takeover-able delegations.

```
subscraper -d example.com -cname -m none -o sub_report.csv -csv
```

Outcome: performs CNAME lookups on the target's subdomains and writes a CSV report flagging names pointing at third-party services that may be claimable.

### Scan many targets from STDIN

Feed a list of root domains and report only active assets.

```
cat domains.txt | subscraper -active -silent
```

Outcome: reads each root domain from STDIN and prints only subdomains that resolve to an IP, in bare-hostname form for piping onward.

### Bruteforce with a custom wordlist and resolver

Extend enumeration beyond indexed sources.

```
subscraper -d example.com -w dns-words.txt -ns 198.51.100.10
```

Outcome: performs DNS bruteforce against example.com using dns-words.txt, with queries sent to the nameserver at 198.51.100.10.

### Audit available modules

See which enumeration sources are compiled in before a run.

```
subscraper -ls
```

Outcome: lists every module by name with a note on which require an API key; keys are stored in ~/.config/subscraper/config.json for reuse.

*No corpus evidence — derived from upstream documentation.*
