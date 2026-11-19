---
name: "wordlistraider"
categories:
  - cracker
version: "1.0~git20200927-0parrot1"
homepage: "https://github.com/GregorBiswanger/WordlistRaider"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "GregorBiswanger/WordlistRaider"
github-stars: 7
github-pushed: "2020-12-08"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wordlistraider

> Filters an existing wordlist down to entries that fit a target's length and character-class rules before you spend requests on it.

## What it's for

WordlistRaider is a Python 3 preprocessing utility for password wordlists: given a source list, it writes a new target list containing only entries whose length falls inside a minimum/maximum window (8 to 100 by default) and whose character classes the target policy allows. By default it drops entries containing digits or special characters; enabling those options (`-n true`, `-s true`) keeps mixed-character passwords instead. The original list is never modified. Use it between downloading a generic wordlist and feeding it to a sprayer or cracker, so candidates the target's password policy would reject anyway never consume a request; it filters only — it neither generates nor mutates passwords.

## Typical use cases

### Keep only policy-compliant passwords

Trim a generic wordlist to the length window a target enforces.

```
wordlistraider -w rockyou.txt -t filtered.txt --min 10 --max 12
```

Outcome: writes every source entry 10–12 characters long to `filtered.txt` — entries containing digits or special characters are excluded unless those classes are enabled — and prints a final `<N> of Passwords written.` count so you can see how much the list shrank.

### Allow digits and special characters

Keep mixed-character candidates when the policy requires complexity.

```
wordlistraider -w rockyou.txt -t filtered.txt --min 8 -n true -s true
```

Outcome: length filtering still applies, but entries with digits and special characters are kept rather than discarded — the right call when the target mandates complex passwords instead of forbidding them.

*No corpus evidence — derived from upstream documentation.*
