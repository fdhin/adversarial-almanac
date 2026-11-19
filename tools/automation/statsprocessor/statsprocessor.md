---
name: "statsprocessor"
categories:
  - blackarch-automation
version: "0.11"
homepage: "https://hashcat.net/wiki/doku.php?id=statsprocessor"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2026-09-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "hashcat/statsprocessor"
github-stars: 199
github-pushed: "2023-10-27"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# statsprocessor

> Markov-ordered word generator built on hashcat statistics files.

## What it's for

statsprocessor (hashcat) generates password candidates in likelihood order
instead of counting order. It loads a hashcat `.hcstat` Markov statistics
file — built from a real-world wordlist — and, constrained by a hashcat
mask, emits first the candidates whose per-position character transitions
the source data made most probable. Because the most likely words come
out first, a truncated run still covers the highest-hit region of the
keyspace. Feed the stream on stdout to any cracker that accepts words from
a pipe. Modern hashcat has per-position Markov ordering built into its
mask attack (`-a 3`), so statsprocessor mainly earns its keep when piping
candidates to other consumers; for plain mask order with no statistics at
all see the related maskprocessor.

## Typical use cases

### Generate the most likely candidates for a mask

```
statsprocessor --pw-min 5 --pw-max 5 hashcat.hcstat ?l?l?l?l?l
```

Outcome: streams five-letter lowercase words (sange, songe, serin, ...)
ordered by Markov likelihood; early output hits real passwords far more
often than counting-order brute force would.

### Restrict output to a length range under a longer mask

```
statsprocessor --pw-min 8 --pw-max 12 hashcat.hcstat ?a?a?a?a?a?a?a?a?a?a?a?a
```

Outcome: emits only 8-12 character candidates from the 12-position mask,
skipping the shorter lengths the mask would otherwise cover.

### Split the keyspace for distributed runs

```
statsprocessor --pw-min 8 --pw-max 8 --skip 1000000 --limit 1000000 -o batch2.txt hashcat.hcstat ?a?a?a?a?a?a?a?a
```

Outcome: skips the first million candidates, writes the next million to
`batch2.txt`; point other nodes at different `--skip`/`--limit` ranges to
parallelize the same keyspace.

### Drop Markov ordering or loosen the statistics

```
statsprocessor --markov-disable hashcat.hcstat ?l?l?l?d?d?d
```

Outcome: candidates come out in plain mask order, ignoring the statistics
file — maskprocessor-like behaviour. Related knobs: `--markov-classic`
uses one table for every position instead of per-position tables, and
`--threshold=NUM` cuts each position's character set down to its NUM most
likely characters, shrinking the keyspace at the cost of rare characters.

*No corpus evidence — derived from upstream documentation.*
