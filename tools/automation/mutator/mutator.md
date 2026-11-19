---
name: "mutator"
categories:
  - blackarch-automation
version: "v0.2.2.r2.g164132d"
homepage: "https://bitbucket.org/alone/mutator/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-12-17"
build-system: make
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# mutator

> Wordlist generator that chains mutations ("with hormones"): case rules, leetspeak, special characters, and appended strings and years.

## What it's for

mutator (by AloneInTheShell, hosted on Bitbucket) turns a handful of seed words into org-specific password candidates. Mutations are applied to the result of earlier mutations — `corporation` becomes things like `C0rp0r4t10n_2012` — through case rules (upper, lower, first-char-upper, swapcase), leet substitution, special-character and hardcoded-string suffixes, and appended dates. Input volume matters: a single word expands to roughly 1 200 candidates and four words to about 4 800, so feed it company names, domains, and admin usernames rather than full dictionaries. It complements generic mangling rules (hashcat/john) when you want deep mutation of very few, high-value words.

## Typical use cases

### Mutate a single seed word

Full mutation chain on one word.

```
mutator -w company -o company.dic
```

Outcome: applies every enabled mutation (case, leet, specials, strings, current-year suffixes) and writes around 1 200 candidate passwords to `company.dic`; feed that file to your cracker of choice.

### Mutate one word per user from a list

Generate a dedicated list per username.

```
for i in $(cat users.txt); do mutator -w "$i" -o "$i.dic"; done
```

Outcome: one mutated dictionary per line of `users.txt` (e.g. `alice.dic`), each containing the full chained mutations of that username — the upstream-recommended pattern for user-targeted spraying lists. The same works with `-f words.txt -o out.dic` to mutate a file of seed words into a single output.

### Keep only light mutations

Skip the long tail of suffixes.

```
mutator -w company -b -o company-basic.dic
```

Outcome: `-b` (basic) restricts output to case and leet mutations only, producing a much smaller, higher-hit-rate list for targets unlikely to use decorated passwords; `-a` does the opposite and keeps only advanced mutations.

### Control year and suffix appending

Tune the appended-date range.

```
mutator -w company -y 2018 -o company-years.dic
mutator -w company -x -s -o company-nosuffix.dic
```

Outcome: `-y 2018` appends every year from 2018 to the current one (a range like `-y 2015-2020` works too); `-x` disables special-character suffixes and `-s` disables hardcoded-string suffixes, letting you assemble exactly the mutation set you want. `mutator -h` lists all options.

*No corpus evidence — derived from upstream documentation (Bitbucket project README).*
