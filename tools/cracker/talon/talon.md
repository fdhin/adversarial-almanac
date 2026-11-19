---
name: "talon"
categories:
  - blackarch-cracker
version: "v3.1.r1.g8acc175"
homepage: "https://github.com/optiv/Talon"
packaged-by: [blackarch]
blackarch-builddate: "2023-08-19"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "optiv/Talon"
github-stars: 441
github-pushed: "2023-08-18"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# talon

> Slow-and-low Active Directory password guesser that spreads attempts across domain controllers and both Kerberos and LDAPS to stay under detection thresholds.

## What it's for

Talon (Optiv, by Tyl0us) performs password guessing against a Windows Active Directory domain while trying not to trigger lockouts or alerts. It first enumerates which users exist using a Kerberos pre-authentication trick that generates zero failed logins, then guesses passwords while rotating between multiple domain controllers and between the Kerberos and LDAPS protocols, sleeping between attempts and honoring a configurable per-account attempt budget. It reads username and password lists, appends results to a file, detects account lockouts mid-run, and prompts before continuing. The packaged upstream is archived; the author points to a maintained continuation at Tylous/Talon on GitHub for current use.

## Typical use cases

### Enumerate valid usernames without lockout risk

Check which accounts exist before guessing anything.

```
talon -D CORP.EXAMPLE.COM -H 198.51.100.10 -Userfile users.txt -E -sleep 1
```

Outcome: sends Kerberos TGT pre-authentication requests with an unsupported encryption type and classifies each account from the KDC error code — `User Exist`, `User Does Not Exist`, or `User's Account Locked` — without causing a single failed authentication, so nothing locks.

### Guess passwords across multiple domain controllers

The core low-and-slow mode.

```
talon -D CORP.EXAMPLE.COM -Hostfile dcs.txt -Userfile users.txt -P 'Password1' -sleep 1 -O results.txt
```

Outcome: each attempt goes to a randomly chosen domain controller from `dcs.txt` over a randomly chosen protocol (Kerberos or LDAPS) with a 1-second pause, splitting the authentication-event load; per-attempt successes and failures print and append to `results.txt`, and when a lockout is detected Talon asks interactively whether to continue.

### Restrict to one protocol with a lockout budget

Adapt to strict domains or filtered protocols.

```
talon -D CORP.EXAMPLE.COM -H 198.51.100.10 -K -Userfile users.txt -P 'Password1' -A 3 -Lockout 60
```

Outcome: attacks only Kerberos (`-L` would force LDAPS instead) against a single domain controller, allowing at most 3 authentication attempts per 60-minute lockout period per account — pacing that stays under a typical lockout policy.

*No corpus evidence — derived from upstream documentation.*
