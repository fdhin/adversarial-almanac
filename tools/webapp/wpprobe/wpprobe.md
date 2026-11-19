---
name: "wpprobe"
categories:
  - blackarch-webapp
version: "0.12.5.r0.g0b9c573"
homepage: "https://github.com/Chocapikk/wpprobe"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-07-19"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "Chocapikk/wpprobe"
github-stars: 972
github-pushed: "2026-10-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 114
gh-activity-checked: "2026-11-09"
---

# wpprobe

> Fast WordPress plugin and theme enumeration mapped to known vulnerabilities.

## What it's for

WPProbe (by Chocapikk) detects the plugins and themes installed on a WordPress site and maps them to known vulnerabilities. Its default `stealthy` mode reads the exposed REST API routes and page HTML, detecting 5000+ plugins without brute-force; `bruteforce` mode probes plugin directories directly for 10k+ coverage; `hybrid` combines both. It ships its own vulnerability database — a prebuilt copy is fetched by `update-db` with no API key needed — and can search that database offline. It enumerates and matches CVEs; it does not attempt exploitation.

## Typical use cases

### Refresh the vulnerability database

```
wpprobe update-db
```

Outcome: downloads the prebuilt plugin/theme vulnerability database (Wordfence and WPScan sources; optional `--api-key` or environment keys for direct fetches), keeping subsequent scans current.

### Scan a target in stealthy mode

```
wpprobe scan -u https://target.example.com
```

Outcome: enumerates plugins via REST API endpoint matching and themes from HTML references, reporting each with version information and matching known vulnerabilities.

### Maximize coverage with hybrid mode

```
wpprobe scan -u https://target.example.com --mode hybrid
```

Outcome: combines REST/HTML candidates with direct plugin-directory file checks, detecting plugins the stealthy mode misses at the cost of more requests (less stealth).

### Scan many targets to a file

```
wpprobe scan -f targets.txt -t 20 -o results.json
```

Outcome: scans the URL list with 20 concurrent threads and writes structured results to `results.json` (CSV also supported) for later reporting.

### Search the local vulnerability database

```
wpprobe search --plugin woocommerce
```

Outcome: lists known vulnerabilities for that plugin from the local database; `--cve`, `--severity`, `--auth`, and `--title` filters narrow the search further.

*No corpus evidence — derived from upstream documentation (README quick start and usage sections).*
