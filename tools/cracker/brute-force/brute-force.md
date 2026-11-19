---
name: "brute-force"
categories:
  - blackarch-cracker
version: "52.78d1d8e"
homepage: "https://github.com/Matrix07ksa/Brute_Force"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Matrix07ksa/Brute_Force"
github-stars: 1845
github-pushed: "2021-10-08"
github-archived: false
github-issues: 47
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# brute-force

> Wordlist login tester for a single account on Gmail, Hotmail, Twitter, Facebook or Netflix.

## What it's for

Brute_Force (packaged by BlackArch as `brute-force`) is a small Python 3 script that replays candidate passwords against one consumer web account's login form. It supports exactly five built-in targets — Gmail, Hotmail, Twitter, Facebook and Netflix — driving their login pages through the `mechanize` browser emulator, one account per run, with candidates taken from a wordlist file or a single value, and an optional proxy list to rotate the source address. There is no generic HTTP-form mode, no SSH or database support, and no handling for CAPTCHAs or multi-factor prompts, so against the live services it mostly demonstrates rate limiting; it remains usable against lab replicas of such login pages.

## Typical use cases

### Replay a wordlist against a webmail account

Test every candidate in a file against one account's login form.

```
brute-force -g user@example.com -l passwords.txt
```

Outcome: each wordlist line is submitted to the Gmail login form in turn and a successful login is reported with the matching password. Swap the account flag for the other services: `-t` (Hotmail), `-T` (Twitter), `-f` (Facebook), `-n` (Netflix). From the source tree run the same arguments as `python3 Brute_Force.py`.

### Check a single candidate password

Test one remembered password without a wordlist.

```
brute-force -g user@example.com -p 'Summer2024!'
```

Outcome: submits the single password to the login form and reports whether it authenticates.

### Spread attempts across proxies

Rotate source addresses for a longer run.

```
brute-force -f user -l passwords.txt -X proxies.txt
```

Outcome: login attempts are issued through the HTTP proxies listed one per line in proxies.txt instead of the local address.

*No corpus evidence — derived from upstream documentation.*
