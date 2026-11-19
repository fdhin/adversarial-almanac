---
name: "pwned-search"
categories:
  - blackarch-recon
version: "40.04c1439"
homepage: "https://github.com/mikepound/pwned-search"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Unlicense
meta-checked: "2026-11-09"
github-repo: "mikepound/pwned-search"
github-stars: 695
github-pushed: "2024-07-16"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pwned-search

> Minimal k-anonymity checker for the Pwned Passwords API — feed it passwords on stdin or as arguments, get exposure counts.

## What it's for

This is Mike Pound's compact demonstration-quality script (packaged as the `pwned-search` wrapper around `pwned.py`) that checks candidate passwords against the HIBP Pwned Passwords corpus. It implements k-anonymity correctly: only the first five characters of each SHA-1 hash are sent, so the password never leaves the machine. It checks passwords only — not email accounts or breaches; for account/breach lookups see the `pwned` note. Reads one password per line from stdin (typed, redirected, or piped) or takes passwords as command-line arguments.

## Typical use cases

### Check a single password

One-off exposure check from the command line.

```
pwned-search Password1
```

Outcome: prints the password's suffix (not the password itself) and how many times it appears in the corpus; anything above zero should never be used. Note the argument form leaks the password into shell history and process listings.

### Check a list of passwords from a file

Audit a whole candidate list at once.

```
pwned-search < password-list.txt
```

Outcome: reads one password per line from the redirected file and prints an exposure count for each — the cleanest form for bulk checking with no history leakage.

### Pipe generated candidates through it

Test the output of another command without a temp file.

```
cat default-passwords.txt | pwned-search
```

Outcome: consumes stdin from the pipeline and reports counts per line; anything generating password candidates (mutators, parsers) can feed it directly.

No corpus evidence — derived from upstream documentation.
