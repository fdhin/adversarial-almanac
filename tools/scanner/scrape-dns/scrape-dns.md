---
name: "scrape-dns"
categories:
  - blackarch-scanner
version: "58.3df392f"
homepage: "https://github.com/304GEEK/Scrape-DNS"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "304GEEK/Scrape-DNS"
github-stars: 55
github-pushed: "2014-05-15"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scrape-dns

> Shell scripts that probe a DNS server for interesting cached entries to reveal what its users have been resolving.

## What it's for

Scrape (Rob Dixon / 304geek, Accuvant Labs) queries a target resolver for large lists of hostnames and watches which answers come back already cached: a cached entry means somebody using that resolver recently looked that name up. `scrape.sh` drives the lookup modes — bundled wordlists cover antivirus update servers (Common AV Mode), adult sites (Obscene Mode) and everything (All Mode), or you supply your own list; the bundled `Timing-Scrape.sh` variant infers cache state from response timing instead of answer contents. It is cache snooping only — it reads resolver behavior, it does not attack the resolver. Aim it only at resolvers you are authorized to assess.

## Typical use cases

### Sweep a resolver's cache with the default lists

Ask what the resolver's users have been resolving.

```
scrape-dns -t 198.51.100.10 -a
```

Outcome: `-a` (All Mode) fires the bundled hostname lists at the target DNS server and reports which entries were served from cache; `-u` (Common AV Mode) restricts the run to antivirus-update hostnames and `-o` (Obscene Mode) to the adult-site list; `-h` prints the usage.

### Probe with a custom hostname list

Test cache hits for names of your choosing.

```
scrape-dns -t 198.51.100.10 -i custom.list
```

Outcome: imports your file and reports which of its entries are cached on the target resolver.

### Infer cache state from response timing

The timing-based companion.

```
/usr/share/scrape-dns/Timing-Scrape.sh 198.51.100.10 custom
```

Outcome: measures per-query response times (modes: custom, porn, updates, all) to infer which names hit the cache, complementing scrape.sh's answer-based detection.

*No corpus evidence — derived from upstream documentation (304GEEK/Scrape-DNS README; packaged paths from the BlackArch recipe).*
