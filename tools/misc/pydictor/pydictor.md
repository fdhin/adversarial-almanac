---
name: "pydictor"
categories:
  - blackarch-misc
version: "100.4a77f0a"
homepage: "https://github.com/LandGrey/pydictor"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "LandGrey/pydictor"
github-stars: 3654
github-pushed: "2024-12-05"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pydictor

> Highly configurable Python wordlist generator covering charset enumeration, rule-based extension, social-engineering profiles, plugins and wordlist post-processing.

## What it's for

pydictor (by LandGrey) builds dictionaries for brute-force attacks in one place: classic charset/length enumeration (`-base`, `-char`), permutation of arbitrary chunks (`-chunk`), config-file and pattern-driven generation (`--conf`), rule-based extension of seed words (`-extend`), a full interactive social-engineering dictionary builder (`--sedb`), and plugin generators for birthdays, ID-card fragments and web-page keywords (`-plug`). Output can be filtered by length, character occurrence counts, character types, regex and repeat counts, leet-transformed, and encoded on the way out (b64, md5, sha1, url, and more). It also ships wordlist maintenance tools (`-tool`) to combine, dedupe, count, filter and securely shred lists. Runs on Python 2.7 and 3.x; the BlackArch package wraps it as the `pydictor` command.

## Typical use cases

### Enumerate a charset with a fixed length

```
pydictor -base d --len 4 4
```

Outcome: generates every 4-digit numeric candidate (0000-9999) and reports the output file it wrote; `-base` accepts mixes such as `dLc` (digits, letters, special characters), and `--len min max` sets the length range.

### Extend seed words into a targeted list

```
pydictor -extend bob --level 4 --len 4 12
```

Outcome: takes the seed word "bob", applies the extension rules configured in funcfg/extend.conf up to rule level 4, and emits derived candidates 4-12 characters long — the fast path from one known token to a themed list.

### Build a social-engineering dictionary interactively

```
pydictor --sedb
```

Outcome: opens the `pydictor SEDB>>` console, where you `set` target facts (name, birthday, phone numbers, email, nickname, ID card, used passwords) and `run` generates a personalized wordlist from them.

### Generate date-range passwords from a plugin

```
pydictor -plug birthday 19800101 20001231 --len 6 8
```

Outcome: emits date-form passwords across the given date range, filtered to 6-8 characters — the typical shape of birthdate-based credentials.

### Harvest keywords from a web page

```
pydictor -plug scratch http://www.example.com
```

Outcome: fetches the page, extracts its keywords, and builds a wordlist from the site's own vocabulary (site-themed passwords) without any manual keyword entry.

### Deduplicate an existing wordlist

```
pydictor -tool uniqifer /tmp/dicts.txt --output /tmp/uniq.txt
```

Outcome: reads the input list, removes duplicate entries, and writes the unique candidates to /tmp/uniq.txt; sibling tools include counter (frequency counting), handler (filter/encode a list), comparer, combiner and shredder (secure delete).

*No corpus evidence — derived from upstream documentation.*
