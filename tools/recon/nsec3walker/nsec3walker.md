---
name: "nsec3walker"
categories:
  - blackarch-recon
version: "20101223"
homepage: "https://dnscurve.org/nsec3walker.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-12-19"
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "unsecured-company/nsec3walker"
github-stars: 13
github-pushed: "2026-09-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# nsec3walker

> Classic NSEC3 zone walker from dnscurve.org: `collect` pulls a zone's hashed NSEC3 chain, `unhash` cracks it offline to recover the hidden names.

## What it's for

nsec3walker (dnscurve.org, 2010-12-23 release) demonstrates that DNSSEC's NSEC3 "hashed" denial-of-existence does not hide zone contents. It is a two-stage C pipeline: `collect` walks the NSEC3 chain of a signed zone with one DNS query per NSEC3 record (finding random names that hash into each unclaimed interval), and `unhash` works entirely offline, hashing candidate names from a dictionary (by default trigraph-ordered from `/usr/share/dict/words`; an alternative dictionary can be given as an argument) to reverse the collected hashes into real names. The point is bandwidth efficiency: recovering tens of thousands of names costs millions of local hash computations but only a few hundred thousand DNS packets — orders of magnitude quieter than guessing names with live queries. Administrators run it against their own zones to measure how much "private" data NSEC3 leaks; zone owners fix this only by minimizing the zone or dropping NSEC3. The Python alternative [[nsec3map]] covers both NSEC and NSEC3 with a similar collect-then-crack strategy.

## Typical use cases

### Collect a zone's NSEC3 chain

Pull the hashed chain from the zone's own servers.

```
collect example.com > example.com.collect
```

Outcome: walks `example.com`'s NSEC3 chain via its authoritative servers, writing one line per recovered hash interval to `example.com.collect`; progress/stat lines (`stats <gaps> <computations> <packets> <intervals> <estimated-names>`) appear in the output as it runs. Add a nameserver argument (`collect example.com 198.51.100.10`) to target a specific server.

### Unhash the collected chain into real names

Recover the actual hostnames offline.

```
unhash < example.com.collect > example.com.unhash
```

Outcome: with zero network traffic, hashes dictionary-ordered name guesses against every collected interval and appends each recovered name to `example.com.unhash` (progress lines report names found and hash count, e.g. "found 199 private NSEC3 names (38%)"); a larger or domain-tuned dictionary can be specified as an argument on the unhash command line to improve the recovery rate.

*No corpus evidence — derived from upstream documentation (collect/unhash workflow from the dnscurve.org nsec3walker page, the packaged upstream).*
