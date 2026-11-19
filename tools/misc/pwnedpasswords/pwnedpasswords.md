---
name: "pwnedpasswords"
categories:
  - blackarch-misc
version: "3.0.0.r7.ge33d4bb"
homepage: "https://github.com/lionheart/pwnedpasswords"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "lionheart/pwnedpasswords"
github-stars: 53
github-pushed: "2025-11-06"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pwnedpasswords

> Python library and CLI that checks a password against Troy Hunt's Pwned Passwords breach corpus without the password ever leaving the machine.

## What it's for

Password audits and policy checks need to answer "has this plaintext appeared in a breach?" without leaking the value being checked. pwnedpasswords (by Dan Loewenherz / Lionheart Software and contributors) wraps the Have I Been Pwned Pwned Passwords API using its k-anonymity range endpoint: only the first five characters of the password's SHA-1 hash are sent, and the API answers with the matching hash suffixes, so the plaintext never crosses the network. Input that already looks like a SHA-1 hash (`[0-9a-fA-F]{40}`) is detected and passed through unhashed — force hashing of hash-looking input with `plain_text=True` / `--plain-text`. It returns how many times the password appears in the corpus (0 = unseen). It is a checker, not a cracker: it reports exposure and nothing more.

## Typical use cases

### Check a single password from the command line

```
pwnedpasswords 123456password
```

Outcome: prints the number of times that password appears in the Pwned Passwords database (for example `2452`); the exit status is log10(count)+1, so 0 means not found and larger means more exposed — scripts can gate on it.

### Check without the password landing in shell history

```
pwnedpasswords --stdin
```

Outcome: reads the password from stdin, prints the breach count, and nothing is recorded in the history file.

### Check from Python with k-anonymity

```
python -c 'import pwnedpasswords; print(pwnedpasswords.check("mypassword"))'
```

Outcome: returns the breach count as an integer via the k-anonymous range endpoint; pass `anonymous=False` to use the faster (non-anonymous) search endpoint instead.

### Pull a raw hash-prefix bucket

```
python -c 'import pwnedpasswords; print(pwnedpasswords.range("098765"))'
```

Outcome: returns a dictionary mapping every SHA-1 suffix sharing that 5-character prefix to its frequency count — the underlying k-anonymity primitive, useful for offline analysis of prefix buckets.

*No corpus evidence — derived from upstream documentation.*
