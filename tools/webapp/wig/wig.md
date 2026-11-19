---
name: "wig"
categories:
  - blackarch-webapp
version: "574.d5ddd91"
homepage: "https://github.com/jekyc/wig"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:BSD
meta-checked: "2026-11-09"
github-repo: "jekyc/wig"
github-stars: 551
github-pushed: "2018-05-29"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wig

> Fingerprints web applications and CMSes — with version probabilities — via file checksums and string matching, and guesses the server OS from headers.

## What it's for

wig (WebApp Information Gatherer, jekyc) identifies which content-management system or administrative application a web server runs and pins its version: it matches checksums and strings of known files across a large fingerprint database, calculates a score per candidate CMS from the number and weight of hits, and displays the most probable versions for each detection. It also guesses the operating system behind the site by matching the Server and X-Powered-By response headers against a bundled database of known header values (Windows versions, Linux distributions). By default it stops after the first CMS is version-detected to limit traffic to the target; -a overrides that. Python 3, usable both as a CLI and as an importable library.

## Typical use cases

### Fingerprint a site's CMS and version

The standard one-shot scan.

```
wig -q http://target.example.com
```

Outcome: scans the target without prompting (-q) and prints each detected CMS with its most probable version(s) plus the OS guess derived from response headers.

### Scan a list of targets

Batch recon.

```
wig -q -l targets.txt -t 10
```

Outcome: reads one URL per line from targets.txt, scans them across 10 threads, and prints a fingerprint block per site.

### Keep testing all fingerprints

Catch layered or secondary apps.

```
wig -a -N http://target.example.com
```

Outcome: continues past the first CMS detected and tests all known fingerprints (-a) with caching disabled (-N) — more traffic, but it surfaces applications layered behind the main CMS.

### Save results as JSON

Machine-readable output.

```
wig -q -w wig-results.json http://target.example.com
```

Outcome: dumps the scan results into wig-results.json, alongside the human-readable console output, for later programmatic processing.

No corpus evidence — derived from upstream documentation (README/help screen of jekyc/wig).
