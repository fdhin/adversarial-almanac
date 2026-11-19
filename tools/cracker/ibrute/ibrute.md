---
name: "ibrute"
categories:
  - blackarch-cracker
version: "12.3a6a11e"
homepage: "https://github.com/hackappcom/ibrute/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hackappcom/ibrute"
github-stars: 1323
github-pushed: "2020-03-05"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ibrute

> 2014 proof-of-concept AppleID password brute forcer via the Find My iPhone API

## What it's for

ibrute is the hackapp.com proof-of-concept for the 2014 Find My iPhone authentication weakness: the service API accepted unlimited password attempts without triggering the lockout that protected Apple's other login endpoints, so a plain dictionary run could recover an AppleID password. It is a deliberately minimal Python 2 script with no command-line options — it reads Apple IDs from `mails.txt` and candidates from `passlist.txt` in its own directory and fires HTTP Basic attempts at the API. Apple patched the missing rate limit shortly after disclosure, so against the current service every attempt is simply rejected (401); the script now matters as the reference exploit for that bug rather than as a working attacker, and it needs a Python 2 interpreter to run at all.

## Typical use cases

### Run the PoC against prepared lists

```
cd /usr/share/ibrute && echo "user@example.com" > mails.txt && ibrute
```

Outcome: prints `Working with: <appleid>` followed by `Trying: <appleid> <password>` for each candidate from the shipped passlist.txt (one `index password` pair per line; the second whitespace-separated field is used as the password), and `Got It!: <appleid> <password>` on a valid credential or `We are blocked!` when the endpoint cuts the run off. On the patched Apple service the loop completes with 401s and no findings — which is itself the demonstration that the flaw is closed.

*No corpus evidence — derived from upstream documentation.*
