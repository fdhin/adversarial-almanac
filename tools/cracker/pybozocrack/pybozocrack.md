---
name: "pybozocrack"
categories:
  - blackarch-cracker
version: "87.ceb0cd9"
homepage: "https://github.com/ikkebr/PyBozoCrack"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ikkebr/PyBozoCrack"
github-stars: 256
github-pushed: "2026-01-22"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pybozocrack

> Cracks MD5 hashes by web-searching them — near-zero CPU, no tables, no brute force.

## What it's for

PyBozoCrack is an MD5 password cracker that computes almost nothing: for each hash it runs a web search and checks whether the plaintext appears in the first page of results, relying on the fact that leaked MD5 password lists are indexed all over the web. It is depressingly effective against passwords that have ever appeared in a public dump, and useless against anything genuinely new or salted. Reach for it as the cheapest first pass over a pile of unsalted MD5 hashes (leaked user tables, default-password lists) before spending CPU on a real cracker — and treat any password it finds as proof the hash scheme was too weak to matter.

## Typical use cases

### Look up a single MD5 hash

Resolve one hash without preparing any files.

```
pybozocrack -s fcf1eed8596699624167416a1e7e122e
```

Outcome: performs the web search and prints the result as `hash:plaintext` (this example resolves to `octopus`); if nothing is found online, the hash is reported as uncracked.

### Crack a whole file of hashes

Run the search over a mixed file, e.g. `user:hash` lines exported from a leak.

```
pybozocrack -f hashes.txt
```

Outcome: prints `Loaded N unique hashes` followed by one `hash:plaintext` line per hash it resolves; the input file has no required format — every string that looks like an MD5 hash is picked up automatically, one per line.

*No corpus evidence — derived from upstream documentation.*
