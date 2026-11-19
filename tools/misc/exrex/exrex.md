---
name: "exrex"
categories:
  - blackarch-misc
version: "151.c3882c7"
homepage: "https://github.com/asciimoo/exrex"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: python
license: AGPL3
aliases: ["python2-exrex"]
meta-checked: "2026-11-09"
github-repo: "asciimoo/exrex"
github-stars: 961
github-pushed: "2026-10-26"
github-archived: false
github-issues: 32
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# exrex

> Turn a regular expression into the strings it matches — enumerate all of them, sample randomly, count, or simplify.

*No corpus evidence — derived from upstream documentation.*

## What it's for

exrex (by Adam Tauber, asciimoo) works in the inverse direction of grep: given a regular expression, it generates strings that match it. It enumerates every matching string (bounded for infinite parts like `+` and `*`, where a limit caps the repetition size), draws random matching strings, counts the total number of matches, and simplifies equivalent regexes. Pure Python with no external dependencies, usable as a CLI or an importable module; generation is generator-based, so memory stays flat even for huge match sets. Use it to build targeted candidate lists from known formats — usernames, card/account number patterns, token shapes, URL paths. It does not match or search text; if you need "which lines match this regex", that is not this tool.

## Typical use cases

### Enumerate every matching string

Expand a known format into a full candidate list.

```
exrex 'user(a|b|c)[0-9]{2}'
```

Outcome: prints every string the regex matches, one per line (usera00 … userc99) — ready to pipe into another tool or save as a wordlist.

### Generate one random matching string

Sample a realistic value instead of enumerating.

```
exrex -r '\d{4}-\d{4}-\d{4}-\d{4}'
```

Outcome: prints a single random string matching the pattern (e.g. a card-shaped `3096-7886-2834-5671`) — repeat in a loop for seeded or bulk test data.

### Count the matching strings

Size a keyspace before generating it.

```
exrex '[01]{10}' -c
```

Outcome: prints the number of distinct matching strings (1024 here) — a quick check whether full enumeration is tractable or random sampling is the better plan.

### Bound an infinite regex and cap the output

Enumerate a bounded slice of an unbounded pattern.

```
exrex -l 2 -m 10 '[a-z]+'
```

Outcome: limits infinite repetition parts to a maximum size (`-l`, default 20) and stops after 10 generated strings (`-m`) — turns `+`/`*`/`{n,}` patterns into a finite, predictable sample; add `-o words.txt` to write the results to a file instead of stdout.

### Simplify an equivalent regex

Rewrite verbose alternatives into a compact class.

```
exrex -s '(ab|ac|ad)'
```

Outcome: prints a simplified, equivalent expression — `(a[bcd])` — useful for collapsing hand-written alternations.
