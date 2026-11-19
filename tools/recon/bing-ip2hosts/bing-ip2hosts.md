---
name: "bing-ip2hosts"
categories:
  - blackarch-recon
version: "1.0.5"
homepage: "http://www.morningstarsecurity.com/research/bing-ip2hosts"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-10-20"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "urbanadventurer/bing-ip2hosts"
github-stars: 139
github-pushed: "2022-05-03"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bing-ip2hosts

> Enumerate every hostname Bing has indexed for an IP — the virtual-host list behind shared hosting.

## What it's for

A Bash scraper (Andrew Horton, urbanadventurer) that exploits Bing's unique search-by-IP feature (`IP:198.51.100.10`) to list the hostnames indexed for an address. It is the standard pivot from an IP to the websites hosted on it — subdomains, related domains and co-tenants sites in shared hosting — during recon or bug bounty. The scraping is "smart": it keeps walking result pages (thousands of results) until a configurable number of consecutive pages yield nothing new, and it appends the magic dot that stops Bing returning empty result sets. No API key is needed; only `wget` is required. Caveat: it depends on Bing still honoring `IP:` queries, which the search engine may throttle or change.

## Typical use cases

### List hosts for one IP

```
bing-ip2hosts 198.51.100.10
```

Outcome: scrapes Bing's results for that address page by page and prints each discovered hostname prefixed with its URL scheme; a progress view shows pages scraped and new hosts found.

### Clean hostnames only

```
bing-ip2hosts -u 198.51.100.10
```

Outcome: prints bare hostnames without the `http(s)://` prefix — directly feedable into other tools or a hosts file.

### Batch IPs from a file into a report

```
bing-ip2hosts -i ips.txt -o hosts.txt
```

Outcome: reads every IP (or hostname) from the input file, scrapes each in turn, and writes the collected hostnames to the output file — the overnight-run shape for expanding a CIDR's worth of addresses.

### CSV output with the IP attached

```
bing-ip2hosts -c 198.51.100.10
```

Outcome: emits `ip,hostname` lines, keeping the mapping between address and discovered site so multi-IP scans stay attributable.

*No corpus evidence — derived from upstream documentation.*
