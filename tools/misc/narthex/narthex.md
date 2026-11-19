---
name: "narthex"
categories:
  - blackarch-misc
version: "v1.2.r7.g8b78746"
homepage: "https://github.com/MichaelDim02/Narthex"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-14"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "MichaelDim02/Narthex"
github-stars: 211
github-pushed: "2024-08-15"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# narthex

> Minimal, modular Unix-style toolkit for building personalized password dictionaries from a handful of seed words.

## What it's for

When a target's likely passwords derive from known keywords (names, projects, dates), a small personalized dictionary beats a huge generic one. Narthex is a suite of small C and shell programs that each perform one transformation on a word stream and follow the Unix text-stream philosophy, so they compose with pipes: `nhance` adds capitalization variants, `ncom` creates line combinations, `nleet` substitutes leet characters, `ninc` appends numeric increments, `nrev` appends reversed lines, `napp` appends characters or words before/after lines, and `nclean` filters out passwords that do not meet your criteria. `nwiz` is an interactive wizard that chains the tools for you by asking for information about the target; `ngen` generates permutations of a given length and character set (still under development). It has no runtime dependencies.

## Typical use cases

### Build a dictionary with the interactive wizard

Let the suite interview you and chain itself.

```
nwiz
```

Outcome: prompts for information about the target (keywords, names, numbers) and combines the Narthex tools automatically, writing the finished dictionary to the current directory.

### Compose a pipeline from seed words

Grow a few keywords into a targeted wordlist by hand.

```
cat words.txt | nhance -f | ncom | nleet a:@ | ninc 1 30 > dictionary.txt
```

Outcome: takes one keyword per line, adds capitalization variants, combines them, leet-substitutes a→@, and appends numeric suffixes 1 through 30 to each line, writing the expanded list to dictionary.txt.

### Scale a pipeline up for a bigger list

Widen the combination ranges and add more transforms.

```
cat words.txt | nhance -f | ncom -d .- | ninc 1 200 -d .- | nrev | nleet | napp -c '@#$' > dict1.txt
```

Outcome: applies the same building blocks with wider increment/combination ranges (`-d .-` preserves separator characters), reversed variants, full leet substitution and special-character suffixes; the upstream README reports ~2.28 million passwords (38 MB) generated this way from just four seed keywords.

*No corpus evidence — derived from upstream documentation.*
