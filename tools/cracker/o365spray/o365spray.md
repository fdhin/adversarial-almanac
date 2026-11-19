---
name: "o365spray"
categories:
  - blackarch-cracker
version: "160.28d8d1b"
homepage: "https://github.com/0xZDH/o365spray"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "0xZDH/o365spray"
github-stars: 1068
github-pushed: "2024-11-06"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# o365spray

> Modular username enumeration and password spraying toolkit for Microsoft Office 365.

## What it's for

o365spray validates whether a domain uses Office 365, enumerates valid usernames, and password-sprays those users. It reimplements a collection of public techniques behind one CLI: enumeration via office.com logins, oAuth2, Autologon, RST and OneDrive; spraying via oAuth2 (default), ActiveSync, Autodiscover, Autologon, the reporting API, RST and ADFS. Spraying is lockout-aware — you state the attempts per user (`--count`) and the lockout reset window in minutes (`--lockout`), and the run aborts automatically once `--safe` (default 10) lockouts are observed. It targets O365/Azure AD authentication; for brute-forcing an on-premises Outlook Web App host see the outlook-webapp-brute note. HTTP traffic can be throttled and randomized (`--sleep`, `--jitter`, `--rate`, `--useragents`) or routed through a FireProx-managed API gateway (`--proxy-url`) to rotate source addresses.

## Typical use cases

### Confirm a domain runs Office 365

Cheap pre-check before any credential testing.

```
o365spray --validate --domain corp.example.com
```

Outcome: the default getuserrealm validation module queries the domain and reports whether it is present in O365 (managed or federated); no authentication attempts are made, so it is safe to run first.

### Enumerate valid usernames

Find which candidate accounts exist before spraying.

```
o365spray --enum -U users.txt --domain corp.example.com
```

Outcome: the default `office` enumeration module tests every username in `users.txt` and writes the usernames that respond as valid to a results file in the output directory; swap techniques with `--enum-module` (autologon, oauth2, onedrive, rst).

### Password spray with lockout discipline

One password per user per window.

```
o365spray --spray -U users.txt -P passwords.txt --count 1 --lockout 15 --domain corp.example.com
```

Outcome: each password from `passwords.txt` is tried once against every user, then the tool waits out the 15-minute lockout window before the next round; valid credentials are written to the output directory and the run stops early if the observed lockout count reaches `--safe`.

### Spray through a rotated-IP FireProx gateway

Evade per-source rate limiting.

```
o365spray --spray -U users.txt -P passwords.txt --spray-module activesync --proxy-url https://ATTACKER_API_ID.execute-api.us-east-1.amazonaws.com/fireprox --domain corp.example.com
```

Outcome: authentication requests are sent through the FireProx-created API Gateway URL (built against the base URL matching the chosen spray module), so the tenant sees rotating source addresses; successes are reported as in a direct spray.

*No corpus evidence — derived from upstream documentation.*
