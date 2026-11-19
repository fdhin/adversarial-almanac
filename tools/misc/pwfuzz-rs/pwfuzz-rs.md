---
name: "pwfuzz-rs"
categories:
  - blackarch-misc
version: "v0.2.0.r1.gac56f42"
homepage: "https://github.com/mttaggart/pwfuzz-rs"
packaged-by: [blackarch]
blackarch-builddate: "2025-03-29"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "mttaggart/pwfuzz-rs"
github-stars: 13
github-pushed: "2025-03-21"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pwfuzz-rs

> Rust filter that mutates an existing password wordlist — appends, case flips, insertions, random numbers — for brute-forcing non-hashable logins.

## What it's for

Hashcat-style rule attacks assume you can test candidates against a hash locally; against a web login, an API or anything rate-limited you just need a better candidate list. pwfuzz-rs (by Michael Taggart) takes a wordlist plus a JSON rules file and prints every mutation to stdout, so the stream can be piped into any brute-forcing tool: append `!` or a year, prepend digits, upper/lowercase the word, insert a character at an index, or attach random numbers. The `-i` flag applies the rule set to its own output iteratively, compounding simple rules into deep variations. The author calls it a working experiment rather than a maintained product; the GitHub repository is archived and development continues at Codeberg (`https://codeberg.org/mttaggart/pwfuzz-rs`), which is now the live upstream.

## Typical use cases

### Mutate a wordlist with a rules file

```
pwfuzz-rs -w wordlist.txt -r rules.json
```

Outcome: prints the original words followed by every mutation the rules produce, to stdout — redirect to a file or pipe straight into the login brute-forcer (`pwfuzz-rs -w wordlist.txt -r rules.json > mutated.txt`).

### Define the mutation rules

```
{ "author": "user", "rules": [ { "Append": "!" }, "Upper", { "Insert": ["%", 4] }, { "AppendRandom": 100 } ] }
```

Outcome: with this rules.json, `letmein` yields `letmein!`, `LETMEIN`, `letm%ein` and `letmein20`-style random-suffixed variants; the full rule set is Append, Prepend, Upper, Lower, Insert, AppendRandom and PrependRandom.

### Compound mutations over iterations

```
pwfuzz-rs -w wordlist.txt -r rules.json -i 3
```

Outcome: applies the rules to their own output three times; the README's 3-word sample list grows to roughly 1500 unique passwords with the sample rules.

*No corpus evidence — derived from upstream documentation.*
