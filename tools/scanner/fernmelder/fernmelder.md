---
name: "fernmelder"
categories:
  - blackarch-scanner
version: "0.4s2.r2.g030212e"
homepage: "https://github.com/stealth/fernmelder"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-25"
build-system: make
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "stealth/fernmelder"
github-stars: 88
github-pushed: "2024-02-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fernmelder

> Asynchronous mass DNS resolver from stealth: pipe a list of hostnames in and it pipelines the queries through recursive resolvers you choose, printing dig-style results.

*No corpus evidence — derived from upstream documentation.*

## What it's for

fernmelder resolves very large lists of names by talking directly (raw UDP) to recursive DNS servers of your choosing — one or more, given with `-N` — rather than relying on the system resolver. It is asynchronous in the mass-DNS sense: it fires several queries in a row before reading the replies back (`-s` controls the burst size, `-S` a microsecond pause between sends), which is what makes bulk resolution fast and what the pacing flags exist to tame. Lookups can run over IPv4 or IPv6 transport and ask for A records (default) or AAAA (`-Q`). Hostnames are read from standard input, one per line, and results print in a dig-like listing. It resolves; it does not brute-force or enumerate.

## Typical use cases

### Bulk-resolve a host list through a chosen resolver

```
fernmelder -4 -N 198.51.100.10 < hosts.txt
```

Outcome: every name in `hosts.txt` is queried over UDP to the resolver at 198.51.100.10; output starts with a `; <<>> fernmelder <<>>` banner echoing the run parameters, followed by the name-to-address results, with CNAME chains and NXDOMAIN replies reported (suppress them with `-A`).

### Map names to IPv6 addresses over IPv6 transport

```
fernmelder -6 -Q -N 2001:db8::53 < hosts.txt
```

Outcome: the same pipelined run, but requesting AAAA records and reaching the resolver over IPv6 — the natural form for building IPv6 inventories from existing name lists.

### Throttle the query rate against a slow resolver

```
fernmelder -4 -N 198.51.100.10 -s 32 -S 500 < hosts.txt
```

Outcome: at most 32 requests are in flight per burst and fernmelder sleeps 500 microseconds between `send()` calls, keeping a rate-limited or fragile resolver from dropping the flood.
