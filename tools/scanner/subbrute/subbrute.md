---
name: "subbrute"
categories:
  - blackarch-scanner
version: "77.r30.g07d2925"
homepage: "https://github.com/TheRook/subbrute"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-04-06"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "TheRook/subbrute"
github-stars: 3538
github-pushed: "2022-01-13"
github-archived: false
github-issues: 39
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# subbrute

> Subdomain bruteforcer that resolves through open resolvers to sidestep DNS rate limiting.

## What it's for

SubBrute (TheRook) enumerates subdomains by bruteforcing a large bundled
dictionary (names.txt, ~101k entries, plus a 1,896-entry subs_small.txt for
quick runs) through thousands of public open resolvers — the resolvers absorb
the query volume, circumventing DNS rate-limiting and keeping your own
addresses off the target's name servers. It works as a recursive "DNS spider"
that crawls into the records it finds, filters geolocation-aware wildcards and
misbehaving nameservers, and doubles as a general DNS record enumerator: the
subdomains found in one run can be fed back with `-s` to enumerate TXT, CNAME,
MX or any other record type. The whole engine is also importable as a Python
library (`subbrute.run()`). Runs under Python 2.7 or 3 with dnspython; the
packaged install exposes it as `subbrute`.

## Typical use cases

### Bruteforce a domain's subdomains

Enumerate names for one or more domains.

```
subbrute example.com -o example.names
```

Outcome: the bundled wordlist is resolved for the domain via open resolvers
and discovered subdomains are printed and written to example.names; the
recursive spider then digs through those records for deeper names.

### Work through a list of domains

Enumerate many targets in one run.

```
subbrute -t domains.txt
```

Outcome: each domain in the file (one per line) is enumerated in turn, the
same as a single-domain run; results for all domains stream to stdout.

### Pivot found subdomains into record enumeration

Use a previous run's output to pull other record types.

```
subbrute -s example.names example.com --type TXT
```

Outcome: for every name in example.names, the requested record type (AAAA,
CNAME, SOA, TXT, MX, ...) is resolved and printed as comma-separated lines —
giving SPF records, alias chains or mail routing for the whole discovered
estate.

*No corpus evidence — derived from upstream documentation (TheRook/subbrute
README).*
