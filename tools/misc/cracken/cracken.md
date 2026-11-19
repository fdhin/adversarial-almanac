---
name: "cracken"
categories:
  - blackarch-misc
version: "v1.0.1.r0.g7a325ff"
homepage: "https://github.com/shmuelamar/cracken"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-19"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "shmuelamar/cracken"
github-stars: 381
github-pushed: "2021-11-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cracken

> Fast Rust password wordlist generator with wordlist-aware masks, Smartlist creation and hybrid-mask analysis.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Cracken generates password candidates roughly 2 GB/s per core — faster than
hashcat's maskprocessor — and extends hashcat-style character masks with wordlist
placeholders (?w1, ?w2, ...). Its second face is analysis: it splits leaked
passwords into common subwords ("Smartlists", built with NLP tokenizers) and
expresses passwords as hybrid masks, so you can generate candidates from the most
probable patterns instead of brute-forcing whole character spaces. Output is
plaintext candidates on stdout; cracken does not crack hashes itself — pipe the
candidates into hashcat or John.

## Typical use cases

### Generate candidates from a character mask

Produce every word matching a mask pattern.

```
cracken -o pwdz.lst '?u?l?l?l?l?l?l?d'
```

Outcome: writes all 8-character candidates (one uppercase, six lowercase, one
digit) to pwdz.lst; omit `-o` to stream them to stdout instead.

### Combine wordlists with mask parts

Mangle dictionaries plus per-position charset rules in one pass.

```
cracken --wordlist firstnames.txt --wordlist lastnames.lst --charset '12' '?w1?w2?1?d?d?d'
```

Outcome: emits `<firstname><lastname>` followed by a 1-or-2 and three digits —
dictionary-driven candidates that a plain rule engine would need many rules to
express.

### Build a Smartlist from leaked passwords

Extract the most representative subwords for targeted generation.

```
cracken create -f rockyou.txt -m 50000 --smartlist smart.lst
```

Outcome: writes smart.lst with up to 50,000 subwords mined from the input
passwords; feed it back via --wordlist so ?w placeholders draw from the
highest-signal vocabulary.

### Score a password's hybrid mask

See which representation cracks a pattern faster.

```
cracken entropy -f smart.lst 'HelloWorld123!'
```

Outcome: prints the minimal subword split, the derived hybrid mask
(`?w1?w1?d?d?s`), and hybrid versus plain character-mask entropy in bits — a
lower hybrid entropy marks the smarter generation strategy.
