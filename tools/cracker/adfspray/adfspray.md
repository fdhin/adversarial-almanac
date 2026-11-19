---
name: "adfspray"
categories:
  - blackarch-cracker
version: "6.3d7745d"
homepage: "https://github.com/xFreed0m/ADFSpray"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-02"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "xFreed0m/ADFSpray"
github-stars: 88
github-pushed: "2023-03-12"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# adfspray

> Password spraying against Microsoft online authentication: AD FS forms, NTLM autodiscover and basic auth.

## What it's for

ADFSpray tests credentials against Microsoft-facing web authentication endpoints using one of three methods: `adfs` (AD FS forms login), `autodiscover` (NTLM against the autodiscover XML endpoint) and `basicauth` (HTTP basic authentication). It is built for spraying — a few passwords against many accounts — accepting single values (`-u`, `-p`, `-t`) or files of users, passwords and targets (`-U`, `-P`, `-T`), with fixed (`-s`) or randomised (`-r`) throttling between attempts and CSV output of results. It performs online testing only; always confirm the account-lockout policy before spraying so you do not lock users out.

## Typical use cases

### Spray one password via an AD FS sign-in page

```
adfspray -u user@corp.example.com -p 'Winter2026!' -t https://sso.corp.example.com adfs
```

Outcome: submits the credentials to the AD FS login form and prints the authentication result; a successful password is reported with the account that accepted it.

### Spray users and passwords across listed endpoints over NTLM

```
adfspray -U users.txt -P passwords.txt -T targets.txt autodiscover
```

Outcome: iterates the password list against every user on every target URL using NTLM authentication to the autodiscover endpoint, reporting each accepted pair.

### Throttled spray with CSV results

```
adfspray -s 300 -o results.csv -U users.txt -p 'Company2026!' -t https://reports.corp.example.com basicauth
```

Outcome: waits 300 seconds between attempts to stay under lockout thresholds and writes the results of every attempt to results.csv for review.

*No corpus evidence — derived from upstream documentation.*
