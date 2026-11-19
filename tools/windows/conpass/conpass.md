---
name: "conpass"
categories:
  - blackarch-windows
version: "0.1.1"
homepage: "https://github.com/login-securite/conpass"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "login-securite/conpass"
github-stars: 215
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 28
gh-activity-checked: "2026-11-09"
---

# conpass

> Lockout-aware password spraying for Active Directory — each user is retried only when that user's own effective password policy says the next attempt is safe.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Continuous password spraying over LDAP against a domain. conpass reads every user's effective password policy (domain default or fine-grained PSO) and the live bad-password counter from the PDC Emulator — the DC that actually owns lockout state — then tries each password only once it cannot lock that account out, keeping the spray running while you append new passwords to the watched password file. It reports valid passwords even when the logon is refused for a second reason (expired password, must-change, disabled account, logon-hours restrictions), recognizes previous passwords (N-1/N-2) and the NTLM grace period after a password change, skips Protected Users, and records every attempt in a SQLite database so an interrupted run resumes without re-testing. Caveats by design: a policy it cannot read (e.g. an unreadable PSO) can still cause lockouts, spraying halts at the first lockout, and NT hashes in the password file are only sprayed as NT hashes.

## Typical use cases

### Spray a password list without locking accounts

```
conpass -d example.com -u user -p 'Password1' -D 198.51.100.10 -P passwords.txt
```

Outcome: enumerates enabled users and their effective lockout policies, then sprays `passwords.txt` one password across all users at a time; each user is retried only after their observation window resets the bad-password counter, found credentials are reported, and every attempt is recorded in `~/.config/conpass/conpass.db`. Passwords appended to the file mid-run are picked up; `Ctrl+C` stops the spray.

### Review results and resume later

```
conpass -d example.com --show
```

Outcome: prints the credentials found so far for the domain from the run database; a re-run skips user/password pairs already tested and never re-sprays users whose password was already found.

### Preview policies and targets before spraying

```
conpass -d example.com -u user -p 'Password1' -D 198.51.100.10 --disable-spray
```

Outcome: connects and displays each user's effective password policy and current lockout counters, verifying the spray will be safe, without sending a single authentication attempt. Add `-a` to a real run to also try each username as its own password.

### Spray with no domain account (offline mode)

```
conpass -d example.com -D 198.51.100.10 -U users.txt -t 5 -o 1800 -P passwords.txt
```

Outcome: sprays only the listed users, assuming the supplied policy — 5 attempts allowed, 1800-second observation window — because without domain credentials no real lockout state can be read; conpass then relies on its own count of failures alone, so keep the assumed threshold conservative.
