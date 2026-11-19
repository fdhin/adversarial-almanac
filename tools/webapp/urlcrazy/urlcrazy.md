---
name: "urlcrazy"
categories:
  - blackarch-webapp
version: "v0.8.2.r0.g78bf6e8"
homepage: "http://www.morningstarsecurity.com/research/urlcrazy"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2025-08-02"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "urbanadventurer/urlcrazy"
github-stars: 701
github-pushed: "2025-07-28"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# urlcrazy

> Generates and tests domain typos and variations to detect — or perform — typo-squatting, URL hijacking, phishing and corporate espionage.

## What it's for

URLCrazy (Andrew Horton, morningstarsecurity) is an OSINT tool that generates domain typos and variations for a given domain and tests whether they exist: with default options it checks over 2000 variants per domain across 17 variation classes — character omission, repetition and swap, keyboard-adjacent typos (QWERTY, AZERTY, QWERTZ, DVORAK layouts), 8000+ common misspellings, homophones, homoglyphs, vowel swaps, bit-flipping, missing dots, prefix/suffix and wrong TLD/second-level-domain, among others. For each variant it reports whether the domain is valid, resolves in DNS, appears in use, and (optionally) its popularity. Defensive use: find typo-squatters profiting from your brand. Offensive use: identify typo domains that will receive a target's traffic. It studies only the DNS/registration surface — it does not analyze site content.

## Typical use cases

### Enumerate and test typo domains for a domain

The core run.

```
urlcrazy example.com
```

Outcome: prints a table of thousands of typo variants with columns showing whether each is valid, resolves (DNS) and is in use — the registered, in-use rows are your typo-squatting candidates.

### Add a popularity estimate per variant

Prioritize which squatters actually get traffic.

```
urlcrazy -p example.com
```

Outcome: as above, plus a popularity estimate column per variant, so you can rank which typo domains actually receive visitors.

### Generate keyboard typos for a non-QWERTY layout

Match the layout your audience types in.

```
urlcrazy -k azerty example.com
```

Outcome: adjacent-key typo generation uses the AZERTY layout instead of the default qwerty (qwertz and dvorak are also supported), changing which character-adjacency variants are produced.

### Export results for processing

Get machine-readable output.

```
urlcrazy -f csv -o typos.csv example.com
```

Outcome: writes the variant table as CSV to typos.csv instead of the colored human-readable console table (json is also available as a format).

No corpus evidence — derived from upstream documentation (README of urbanadventurer/urlcrazy 0.8.2).
