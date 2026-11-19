---
name: "enum-shares"
categories:
  - blackarch-scanner
version: "7.97cba5a"
homepage: "https://github.com/dejanlevaja/enum_shares"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "dejanlevaja/enum_shares"
github-stars: 5
github-pushed: "2014-03-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# enum-shares

> Sweeps a network range for SMB shared folders under a chosen account and marks which shares accept writes.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding reachable file shares at scale, and specifically the misconfigured ones. enum_shares takes an IP or CIDR range, authenticates per host with credentials you supply (local or `DOMAIN\\user` form), lists the folders each host exposes to that account, and — its distinguishing feature — can test every discovered share for writability, so "directories this user (or everyone) can write to" fall out of a single run. Results can be written to a log file instead of only the terminal, and the thread count is adjustable when a sweep should be gentler. It enumerates shares and their access only; it does not browse file contents or attack authentication.

## Typical use cases

### Enumerate shares across a subnet

```
enum-shares -t 198.51.100.0/24 -u user -p Password1
```

Outcome: iterates every host in the range, authenticating with the supplied account, and prints each share it can list per host — the raw map of exposed SMB folders. Add `-n 50` to cap worker threads below the default 100 if the sweep must be gentler.

### Find and log writable shares

```
enum-shares -t 198.51.100.0/24 -u DOMAIN\\user -p Password1 -w -o shares.txt
```

Outcome: after enumerating, attempts a write test against each discovered share (`-w`) and records the full run to `shares.txt` (`-o`); the shares that accept writes are the actionable subset — drop locations, misconfigured backups, privilege-escalation paths.
