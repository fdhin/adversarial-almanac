---
name: "country-ip-blocks"
categories:
  - blackarch-wordlist
version: "6365.1ad7ecd"
homepage: "https://github.com/herrbischoff/country-ip-blocks"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-28"
build-system: scripts
license: CC0-1.0
meta-checked: "2026-11-09"
github-repo: "herrbischoff/country-ip-blocks"
github-stars: 920
github-pushed: "2026-03-04"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# country-ip-blocks

> Per-country IPv4 CIDR lists generated hourly from Regional Internet Registry delegation data.

## What it's for

A pure dataset: one file per country (named by its lowercase ISO 3166-1 alpha-2 code, e.g. `de.cidr`), each line an IPv4 CIDR block from that country's RIR delegation data, refreshed hourly upstream. The package installs the files under `/usr/share/country-ip-blocks/`. There is no CLI — you consume the `.cidr` files with whatever tool accepts network lists.

Two caveats direct from the author: the public GitHub repository is archived and the project moved to the author's private git host (herrbischoff.com), so the packaged snapshot reflects its last public state; and the author explicitly states the data is entirely unsuitable for geoblocking, since RIR delegation reflects registration, not real-world location — use it for scoping scans, research and labelling, not access control. (The same author publishes abuse-focused IP blocklists at ipbl.herrbischoff.com for filtering use cases.)

## Typical use cases

### Inspect a country's network inventory

```
ls /usr/share/country-ip-blocks/ | head
wc -l /usr/share/country-ip-blocks/de.cidr
```

Outcome: the first command shows the per-country `.cidr` file naming on your install; the second prints how many CIDR blocks the German delegation data contains. `head de.cidr` shows the actual ranges, one IPv4 network per line.

### Feed registry networks to a scanner

```
head -20 /usr/share/country-ip-blocks/de.cidr > sample-de.cidr
nmap -sn -iL sample-de.cidr
```

Outcome: writes a 20-network scope file from the German list and runs nmap host discovery (no port scan) over it, printing responsive hosts — swap in the country file you need and trim the sample size to keep scans legal and scoped.

*No corpus evidence — derived from upstream documentation.*
