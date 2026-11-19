---
name: "getsids"
categories:
  - blackarch-database
version: "1.0.0"
homepage: "http://www.cqure.net/wp/getsids/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-14"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/getsids"
github-stars: 0
github-pushed: "2025-12-14"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# getsids

> Sends the Oracle TNS listener's `services` command and prints the database SIDs/instances the listener reports.

## What it's for

By Patrik Karlsson (cqure.net). An Oracle TNS listener — normally on TCP 1521 — answers a services query with the list of database services and SIDs it fronts; getsids sends that packet and prints the parsed reply. It does exactly one thing, SID enumeration: no brute forcing (use a SID guesser against listeners that stay silent) and no credential checks. A valid SID is the prerequisite for most further Oracle work, such as building a connect string or guessing default accounts, so this is a first recon step against an exposed listener. The packaged source is a modernized mirror of the original C program that builds with current gcc.

## Typical use cases

### Enumerate SIDs from an exposed listener

```
getsids 198.51.100.10 1521
```

Outcome: connects to the Oracle listener and prints each service/SID it reports (for example the instance name and `PLSExtProc`); every printed SID is a candidate for a follow-up connection attempt. Called with fewer than two arguments it prints its usage banner (`getsids <ip> <port>`) and exits.

*No corpus evidence — derived from upstream documentation.*
