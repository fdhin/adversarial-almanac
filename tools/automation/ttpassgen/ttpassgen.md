---
name: "ttpassgen"
categories:
  - blackarch-automation
version: "133.a06d99d"
homepage: "https://github.com/tp7309/TTPassGen"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "tp7309/TTPassGen"
github-stars: 170
github-pushed: "2026-09-01"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# ttpassgen

> Scriptable rule-based password and wordlist generator that composes charsets, literals and whole dictionary files.

## What it's for

TTPassGen generates password candidate lists from compact, regex-like rules. A rule mixes three element types: character arrays (`[?d]` for digits, `?l`/`?u` for letters, `?s` specials, `?a` all), literal strings, and references (`$0`, `$1`) to wordlist files passed with `--dictlist`. Each element carries a repeat range `{min:max:mode}` so you can express masks of variable length, and results go straight to an output file with optional size-based splitting. Use it when crunch-style masks are enough but you also want to glue company names, years or entire dictionaries onto those masks in one pass. It is a generator only — it does not attack anything itself.

## Typical use cases

### Generate fixed-length numeric PINs

```
ttpassgen -r '[?d]{8:8:*}' pins.txt
```

Outcome: writes every 8-digit combination (00000000–99999999) to `pins.txt`; the `*` repeat mode allows digits to repeat within a word.

### Build a mask with a literal suffix

```
ttpassgen -r '[123]{2:3}xyz' out.txt
```

Outcome: writes all words made of 2–3 characters drawn from {1,2,3} followed by the literal `xyz` (e.g. `12xyz`, `321xyz`) to `out.txt`.

### Combine two wordlists with an optional separator

```
ttpassgen --dictlist "in.txt,in2.txt" --rule '$0[_]?$1' -s " " out.txt
```

Outcome: writes every pairing of a line from `in.txt` with a line from `in2.txt`, with an optional `_` between them, using a space as the output separator — a base list fused with a modifier list.

### Append digits to every base word

```
ttpassgen --dictlist words.txt --rule '$0[?d]{2:2:*}' words-with-years.txt
```

Outcome: takes each word from `words.txt` and appends every 2-digit combination, producing the classic `password87`-style candidates for spraying or cracking.

### Split huge outputs into parts

```
ttpassgen -r '[?l?u?d]{8:10:*}' -p 500 -a big.txt
```

Outcome: generates 8–10 character mixed-charset words and writes them to files split at 500 MB each (`-p`), appending to existing output rather than overwriting (`-a`).

*No corpus evidence — derived from upstream documentation.*
