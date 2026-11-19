---
name: "hakrevdns"
categories:
  - blackarch-recon
version: "49.2a847d3"
homepage: "https://github.com/hakluke/hakrevdns"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-24"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "hakluke/hakrevdns"
github-stars: 1620
github-pushed: "2026-08-05"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# hakrevdns

> Mass reverse DNS: pipe in IPs, get hostnames back — ideal for netblock recon.

## What it's for

hakrevdns performs reverse DNS lookups in bulk: feed it IP addresses on
stdin — a CIDR expanded with prips, a resolved range, anything that produces
one IP per line — and it resolves them concurrently, printing
`ip<TAB>hostname` for each that answers. Its purpose is domain discovery
from infrastructure: a target organization's netblock routinely reveals
dozens of vhosts, subdomains and forgotten project names via PTR records
alone. Tuning knobs cover threads (`-t`), a single custom resolver (`-r`),
a resolver list file (`-R`), TCP instead of UDP (`-P tcp`) and a
domains-only output mode (`-d`); the line-based design pipelines naturally
into HTTP-probing follow-ons. It does not enumerate subdomains of a domain
— it maps addresses to names, the reverse direction.

## Typical use cases

### Reverse-resolve a whole netblock

```
prips 198.51.100.0/24 | hakrevdns
```

Outcome: concurrent PTR lookups for all 256 addresses; each address that
resolves prints as `ip<TAB>hostname`, giving the hostname inventory of the
range.

### Domains-only output through a chosen resolver

```
prips 198.51.100.0/24 | hakrevdns -r 1.1.1.1 -d
```

Outcome: bare hostnames with no IP column, resolved via 1.1.1.1 instead of
the system resolver — pipe through `sort -u` for a deduplicated name list.

### Pipeline into HTTP probing

```
cat ips.txt | hakrevdns -d -t 50 | httprobe
```

Outcome: discovered hostnames flow straight into httprobe, which reports
which of them actually serve HTTP(S) — netblock to live-web list in one
line.

*No corpus evidence — derived from upstream documentation.*
