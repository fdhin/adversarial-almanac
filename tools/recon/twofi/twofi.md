---
name: "twofi"
categories:
  - blackarch-recon
version: "2.0"
homepage: "http://www.digininja.org/projects/twofi.php"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "digininja/twofi"
github-stars: 31
github-pushed: "2022-11-10"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# twofi

> Twitter Words of Interest — build targeted password wordlists from what Twitter says about a company, product or person.

## What it's for

twofi (by Robin Wood, digininja) generates custom cracking wordlists from Twitter. It takes search terms related to the target — company names, product names, people — pulls the tweets mentioning them, and emits a word list sorted by frequency, on the theory that a target's own vocabulary leaks into their passwords. It can equivalently mine the timelines of specific users instead of search terms. Version 2 uses the authenticated Twitter API: you must create a Twitter application and put its keys in `twofi.yml` (path overridable with `--config`), which is the only setup step. Output is the wordlist on stdout (plus optional counts), ready to feed to a cracking tool as a complement to standard dictionaries. It is a wordlist generator, not a cracker.

## Typical use cases

### Build a wordlist from target-relevant terms

Distill tweets about the target into candidate passwords.

```
twofi -t example,corp,product
```

Outcome: searches Twitter for each term, counts word frequencies across the matching tweets and prints the merged list most-common-first — pipe it into a file to use as a rule-friendly cracking dictionary. Note the strict syntax: comma-separated with no spaces after commas, quoting multi-word terms.

### Mine specific users' timelines instead of searches

Use individuals' own tweets as the corpus.

```
twofi -U users.txt
```

Outcome: for each username in the file (one per line; a leading `@` is stripped automatically), pulls as many tweets as the API allows and builds the frequency-sorted wordlist from their combined vocabulary.

### Keep counts and filter short words

Tune the list for limited cracking attempts.

```
twofi -t example,product -c -m 6
```

Outcome: `-c/--count` prefixes each word with its usage count so you can prioritize, and `-m/--min_word_length 6` drops short noise words — fewer, better candidates when guess budget is small.

### Point at a non-default config

Run against a kept-elsewhere API key file.

```
twofi -t example --config /path/to/twofi.yml
```

Outcome: the Ruby script loads its Twitter API credentials from the given YAML instead of expecting `twofi.yml` in the working directory — the way to run twofi from any directory or with per-engagement credentials.

*No corpus evidence — derived from upstream documentation.*
