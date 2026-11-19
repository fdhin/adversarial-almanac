---
name: "maryam"
categories:
  - blackarch-webapp
version: "v.2.5.3.r0.g2bb4ea2"
homepage: "https://github.com/saeeddhqan/Maryam"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "saeeddhqan/Maryam"
github-stars: 1229
github-pushed: "2024-09-19"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# maryam

> OWASP Maryam: a modular OSINT and data-gathering framework.

## What it's for

Maryam (by Saeed Dehqan, an OWASP project) is a modular framework for harvesting publicly available data: it wraps dozens of search engines and OSINT sources behind a uniform `-e <module>` interface. Modules cover DNS reconnaissance (`dns_search`, `dnsbrute`), search-engine harvesting (`google`, `youtube`, and social sources), and meta-search extraction, with framework-level options for proxy, user-agent, thread count, and timeout. The final release is 2.5.3 and the upstream repository is archived — the tool still runs but its scrapers age as the engines they target change. It gathers and correlates data; it does not attack anything.

## Typical use cases

### Pull DNS records from every source at once

```
maryam -e dns_search -d example.com -t 5 --max --api
```

Outcome: queries all available DNS sources with 5 threads (`--max` removes the source cap) and prints the aggregated records as JSON (`--api`); drop `--api` for a table.

### Harvest search-engine results for a query

```
maryam -e google -q "example.com filetype:pdf"
```

Outcome: scrapes the engine for the query and prints the harvested result set — swap the module (`youtube`, and other sources) without changing the calling convention.

### Brute-force subdomains

```
maryam -e dnsbrute -d example.com
```

Outcome: resolves a built-in wordlist against the domain and prints the subdomains that answer.

### Discover what the framework can do

```
maryam -e show modules
```

Outcome: lists every available module by category — the starting point for picking the right `-e` target.

### Serve results over a local web API

```
maryam -e web api 127.0.0.1 1313
```

Outcome: starts Maryam's HTTP API on port 1313 so other tools can query modules programmatically; set framework options the same way with `maryam -e set proxy http://127.0.0.1:8080`.

No corpus evidence — derived from upstream documentation.
