---
name: "blackhash"
categories:
  - blackarch-cracker
version: "0.2"
homepage: "http://16s.us/blackhash/"
packaged-by: [blackarch]
blackarch-builddate: "2020-01-27"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "sepehrdaddev/blackhash"
github-stars: 0
github-pushed: "2020-01-27"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# blackhash

> Builds a compact Bloom-style filter from password-hash dumps and tests other dumps against it.

## What it's for

blackhash condenses a list of password hashes into a small bit-filter that can be stored or shared instead of the full list. A `create` run digests every hash — accepted input formats are a bare `<hash>` per line, `user:hash`, and pwdump-style 7-column account lines — into bit positions derived from MD4/MD5 digests; a `test` run loads the filter and prints only the entries of a new dump whose bits are already set. The typical use is checking a fresh dump against a large known-credentials corpus without keeping or distributing the corpus itself. Matches can include false positives by design, and hashes cannot be recovered from the filter.

## Typical use cases

### Create a filter from a hash dump

```
blackhash dump.txt corp.filter create
```

Outcome: loads every hash from dump.txt and writes corp.filter, reporting "Number of password hashes loaded from file: N" and how many bits were set.

### Test a new dump against the filter

```
blackhash newdump.txt corp.filter test
```

Outcome: prints each `user:hash` line from newdump.txt whose bit positions are set in the filter — the accounts that already appear in the original corpus.

*No corpus evidence — derived from upstream documentation.*
