---
name: "rulesfinder"
categories:
  - blackarch-misc
version: "42.cf8255b"
homepage: "https://github.com/synacktiv/rulesfinder"
packaged-by: [blackarch]
blackarch-builddate: "2023-08-19"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "synacktiv/rulesfinder"
github-stars: 115
github-pushed: "2023-08-18"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rulesfinder

> Machine-learn password mangling rules for John the Ripper or Hashcat from a wordlist and cracked passwords.

## What it's for

rulesfinder (Synacktiv) derives password-mangling rules by learning which transformations actually turn a given dictionary into a corpus of already-cracked passwords. Feed it the wordlist you crack with plus your recovered cleartexts, and it emits a small, high-yield ruleset ordered by effectiveness — typically far better hit rates on that specific target population than generic rulesets. The learning phase is memory-hungry (multi-GB with large cleartext corpora; raise `--minsize` to trade recall for memory, or cap usage with `ulimit -d`) and the tool is explicitly alpha quality. It generates rules only — it does not run cracking sessions. A companion `dumper` binary in the source tree can dump or apply rules against a dictionary.

## Typical use cases

### Learn a targeted ruleset from cracked passwords

Derive a compact ruleset that best reproduces your cracked-password corpus from your wordlist.

```
rulesfinder -w wordlist.txt --cleartexts cracked.txt -n 100 -t 8 --minsize 4 > learned.rule
```

Outcome: reads both files, shows a progress bar while learning, then prints the best rules one per line in John the Ripper syntax to stdout; a rule is kept only if it cracks at least 100 (`-n`) corpus passwords. Load the result in John with `--rules=learned.rule` (or hashcat's `--rules-file`).

### Restrict output to Hashcat-compatible rules

Generate a ruleset guaranteed to be valid in Hashcat's rule dialect, with statistics attached.

```
rulesfinder -w wordlist.txt --cleartexts cracked.txt --hashcat --details
```

Outcome: filters out rules that use John-only functions and annotates each emitted rule with how many corpus passwords it cracks, so weak rules can be pruned before the cracking session.

*No corpus evidence — derived from upstream documentation.*
