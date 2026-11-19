---
name: "attacksurfacemapper"
categories:
  - blackarch-recon
version: "v1.1.r25.g8a402ed"
homepage: "https://github.com/superhedgy/AttackSurfaceMapper"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "superhedgy/AttackSurfaceMapper"
github-stars: 1412
github-pushed: "2024-04-08"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# attacksurfacemapper

> Grow a seed list of domains and IPs into a full attack surface, then passively profile every target found.

## What it's for

AttackSurfaceMapper (by Andreas Georgiou, superhedgy) takes one or more domains, subdomains or IP addresses and expands them into more targets: subdomain bruteforcing and passive lookups, other IPs of the same network block owner, and IPs hosting multiple domain names. Once the list is expanded it profiles targets passively — website screenshots, credential lookups in public breaches, Shodan/Censys port data and LinkedIn employee scraping. Optional API keys (Shodan, Censys, VirusTotal, Hunter.io and others) go into the `keylist.asm` file to enrich results. It is a recon aggregator, not an exploit tool. Run as `attacksurfacemapper` on BlackArch (a wrapper around `asm.py`); Python 3 only.

## Typical use cases

### Expand a domain into a target list and profile it

```
attacksurfacemapper -t example.com -w resources/top100_sublist.txt -o demo_run
```

Outcome: bruteforces subdomains from the bundled wordlist plus passive sources, expands to related IPs and domains, then runs the default passive modules; results land in the `demo_run` output files in the current directory.

### Passive-only mode

```
attacksurfacemapper -t example.com -sth -o demo_run
```

Outcome: `--stealth` restricts the run to OSINT techniques only — no packets are sent directly at the target beyond the passive lookups.

### Capture screenshots of discovered web apps

```
attacksurfacemapper -t example.com -sc -f CSV -o demo_run
```

Outcome: takes a screenshot of every web application found and writes the results in CSV format (instead of the default TXT) alongside the images.

### Recursively expand the target list

```
attacksurfacemapper -t example.com -e -o demo_run
```

Outcome: `--expand` applies the discovery chain recursively — newly found subdomains and IPs feed back into the expansion until no new targets appear.

*No corpus evidence — derived from upstream documentation.*
