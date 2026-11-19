---
name: "netscout"
categories:
  - blackarch-recon
version: "37.2f409df"
homepage: "https://github.com/caio-ishikawa/netscout"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-01"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "caio-ishikawa/netscout"
github-stars: 183
github-pushed: "2024-04-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netscout

> Go OSINT spider: given one seed URL, collects subdomains, directories, endpoints, and files via crawling, DNS zone transfer, BinaryEdge, and Google dorking.

## What it's for

NetScout (caio-ishikawa, Go) expands a single seed URL into an attack-surface inventory. It runs five components: a BinaryEdge API client for historical subdomain data; an AXFR attempt to pull a full zone transfer from the seed's nameservers; a web crawler that walks URLs and directories to a set depth; a SERP/Google-dork client that searches for file types discovered by the crawler; and an optional "deep" scan that downloads URLTeam's shortened-URL archives and checks them for the seed's host (large, multi-minute). Two external APIs are used when their keys are exported (`BINARYEDGE_API_KEY`, `SERP_API_KEY`); the corresponding scans are skippable so the tool works fully offline. Not related to the commercial NetScout network-monitoring products despite the name.

## Typical use cases

### Basic expansion of a seed URL

Collect everything the default pipeline finds for one site.

```
netscout -u https://example.com -d 2 -o netscout.txt
```

Outcome: crawls to depth 2, attempts the zone transfer, queries BinaryEdge and Google dorks (if keys are set), and writes all discovered domains, subdomains, URLs, and files to `netscout.txt`; `-v` additionally prints every URL as it is found.

### Offline run without API keys

Restrict the scan to crawling and zone transfer.

```
netscout -u https://example.com -d 2 -skip-binaryedge -skip-google-dork -o netscout.txt
```

Outcome: skips both external-API scans (and you can add `-skip-axfr` to drop the zone-transfer attempt too), so the run needs no keys and produces the crawler's URL/directory inventory only.

### Polite crawl with headers and cookies

Reach authenticated or rate-limited content without hammering the target.

```
netscout -u https://example.com -d 2 -t 5 -delay-ms 1000 -h "X-API-Key=test,Other=1" -c "session=abc123" -o netscout.txt
```

Outcome: crawls with 5 threads and a 1000 ms delay between requests, sending the given headers (`-h`) and cookies (`-c`) on every request; the harvest lands in the output file as usual.

### Render JavaScript pages during the crawl

Let a headless Chrome resolve script-built links.

```
netscout -u https://example.com -d 2 -headless -lock-host -o netscout.txt
```

Outcome: all crawler requests are routed through a headless Chrome (which must be installed), exposing links that only appear after script execution; `-lock-host` keeps only URLs on the seed's host, pruning third-party noise.

### Include the shortened-URL scan

Check URLTeam's archives for the seed host.

```
netscout -u https://example.com -deep -d 2 -t 5
```

Outcome: downloads the most recent shortened-URL list (hundreds of MB) and reports any shortlink whose destination host matches the seed — expect several minutes of runtime before results appear.

*No corpus evidence — derived from upstream documentation (flags taken from the packaged README's usage reference).*
