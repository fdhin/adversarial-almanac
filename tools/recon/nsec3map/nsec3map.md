---
name: "nsec3map"
categories:
  - blackarch-recon
version: "v0.8.2.r0.gd145b13"
homepage: "https://github.com/anonion0/nsec3map"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "anonion0/nsec3map"
github-stars: 221
github-pushed: "2023-03-06"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nsec3map

> DNSSEC zone enumerator (command `n3map`): walks NSEC/NSEC3 chains to recover every name a "protected" DNS zone actually exposes.

## What it's for

nsec3map (anonion0; installed as `n3map`) enumerates all resource records of a DNSSEC-signed zone by following its NSEC or NSEC3 denial-of-existence chain — the mechanism meant to prove a name does NOT exist leaks the names that do. It auto-detects whether the zone uses NSEC (plain walking) or NSEC3 (hash-interval walking plus local hash computation), resolves the zone's nameservers itself, and can enumerate million-entry zones with few queries, including a zone-size prediction and progress bar. It was written to demonstrate that NSEC3 provides no meaningful protection against zone enumeration, and it ships converters so the recovered NSEC3 hashes can be cracked with John the Ripper or hashcat. Operators use it to measure how much of their zone an attacker can recover; be aware it is query-visible on the target's nameservers (see `--limit-rate`). The standalone C tool [[nsec3walker]] performs the same NSEC3 walk with a different engine.

## Typical use cases

### Walk a zone with auto-detection

Enumerate a DNSSEC zone's names without knowing its chain type.

```
n3map -v -o example.com.zone example.com
```

Outcome: looks up the zone's nameservers, reports "zone uses NSEC records" (or NSEC3), walks the chain, and writes every discovered owner name and its record types to `example.com.zone`; the progress line shows records found and queries sent, and `-v` narrates each step.

### Enumerate an NSEC3 zone against a chosen nameserver

Point the walk at a specific server and watch coverage.

```
n3map -3po example.com.zone 198.51.100.10 example.com
```

Outcome: forces NSEC3 mode (`-3`), uses the nameserver at 198.51.100.10, and shows the predicted zone size and coverage percentage (`-p`) while writing the chain to the output file; rate drops sharply near 100% coverage, so large zones are often stopped manually once coverage suffices.

### Rate-limited or parallel walking

Trade speed for stealth and load control.

```
n3map -3pvo example.com.zone --aggressive 16 --limit-rate 100/s example.com
```

Outcome: sends up to 16 concurrent queries (`--aggressive 16`) while capping the rate at roughly 100 queries/second (`--limit-rate`), finishing faster on high-RTT links without overloading the target nameservers.

### Resume a partial enumeration

Continue where a cancelled run left off.

```
n3map -3pv --input example.com.partial --output example.com.zone --ignore-overlapping example.com
```

Outcome: loads the NSEC3 records already saved in `example.com.partial` and continues walking from there (valid while the zone's salt and iteration count are unchanged); `--ignore-overlapping` keeps the run alive when late responses overlap earlier records instead of aborting.

### Crack the recovered NSEC3 hashes offline

Turn the collected hashes into names with John or hashcat.

```
n3map-johnify example.com.zone example.com.john && john example.com.john
```

Outcome: converts the zone file's NSEC3 records into John the Ripper's NSEC3 format and starts cracking them (the format is built into JtR jumbo); use `n3map-hashcatify example.com.zone example.com.hashcat` and `hashcat -m 8300 example.com.hashcat` for the hashcat route.

*No corpus evidence — derived from upstream documentation (usage examples and options from the packaged README).*
