---
name: "wordlister"
categories:
  - blackarch-misc
version: "56.7457c21"
homepage: "https://github.com/4n4nk3/Wordlister"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "4n4nk3/Wordlister"
github-stars: 122
github-pushed: "2023-12-21"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wordlister

> Minimal Python permutation engine for targeted password cracking: combines words from a seed file up to N per line, then mutates them with l33t, capitalisation, uppercase and append/prepend, bounded by min/max length.

*No corpus evidence — derived from upstream documentation (the upstream repository has since been removed from GitHub; the README preserved in mirrors is the source).*

## What it's for

Turning a handful of words you already know about a target — names, pets, birthdays, company terms — into a focused candidate list instead of cracking against a giant generic wordlist. You supply an input file of words; wordlister generates every permutation up to `--perm` words per line (never repeating a word inside one candidate), applies the mutagens you enable, and filters the output to the length window your cracker cares about. Runs multi-core for speed. It has no rule-file syntax like hashcat or John — the mutagens are fixed (leet, capitalise, uppercase, append, prepend), which is the point: small input, predictable output.

## Typical use cases

### Generate a mutated combination list

```
wordlister --input seed.txt --perm 2 --min 6 --max 32 --leet --cap --up --append 2026 > wordlist.txt
```

Outcome: writes every 1- and 2-word permutation of `seed.txt` to stdout — each also in l33t (`o→0`, `a→4`, …), capitalised and uppercased form, with `2026` appended — keeping only candidates between 6 and 32 characters; the redirect captures them in `wordlist.txt` ready for the cracker.

### Preview the combinatorics before a full run

```
wordlister --input seed.txt --perm 3 --min 8 --max 16 --test 20
```

Outcome: runs only the first 20 iterations in a single process, so you can sanity-check how quickly `--perm 3` explodes the candidate space before committing to the full multi-core run.

### Pin the worker pool

```
wordlister --input seed.txt --perm 2 --min 6 --max 32 --cores 4
```

Outcome: generation runs on exactly 4 processes instead of the automatic pool — useful on shared boxes or when memory is tight during large `--perm` runs.
