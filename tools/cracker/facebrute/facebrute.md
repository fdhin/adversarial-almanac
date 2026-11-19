---
name: "facebrute"
categories:
  - blackarch-cracker
version: "7.ece355b"
homepage: "https://github.com/emerinohdz/FaceBrute"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: Artistic
meta-checked: "2026-11-09"
github-repo: "emerinohdz/FaceBrute"
github-stars: 30
github-pushed: "2012-01-18"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# facebrute

> Dictionary brute-forcer for a Facebook account's login form that backs off while the account is temporarily locked.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Posts the Facebook login form once per password from a wordlist, talking plain HTTP with no browser, and detects the temporary lockout Facebook applies to accounts that keep failing: instead of hammering, it sleeps progressively longer (60 seconds, then more) until the lock releases, then resumes the list. It can route every attempt through a proxy and read the wordlist in a chosen encoding. It only does online form guessing against that one service — no hash cracking, no offline files. It is 2012-era Python 2 against a login endpoint that has long changed shape, so treat it as a template for studying form brute-forcing and lockout back-off rather than a working attack; the interesting, reusable part is the wait-on-lockout loop.

## Typical use cases

### Run a dictionary attack against one account

```
facebrute -u user@example.com -p passwords.txt
```

Outcome: prints `Trying: <password>` for each guess; a working password ends the run with `SUCCESS: <password>`, an exhausted list with `FAILED: None of the provided passwords worked!`. When Facebook starts blocking the account, the script prints `Waiting...` and pauses before continuing.

### Send guesses through a proxy with a non-UTF-8 wordlist

```
facebrute -u user@example.com -p passwords.txt -e latin1 -P 127.0.0.1:8080
```

Outcome: all HTTP traffic goes to the given `host:port` proxy (handy for watching the raw requests in an intercepting proxy), and each password line is decoded with the specified encoding before it is submitted. `-e` defaults to `utf-8`; the proxy argument must be plain `host:port` without a scheme.
