---
name: "gomapenum"
categories:
  - blackarch-cracker
version: "v1.1.0.r114.g5aee194"
homepage: "https://github.com/nodauf/GoMapEnum"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-10-04"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nodauf/GoMapEnum"
github-stars: 497
github-pushed: "2025-09-24"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gomapenum

> One Go binary for user enumeration and password spraying against Azure, O365, OWA, ADFS, Teams, LDAP, Kerberos, SMB and SMTP, plus email harvesting from LinkedIn and search engines.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Bundles the black-box techniques used against an organization's Microsoft-facing (and generic) authentication surfaces under one cobra-style CLI with subcommands per service. First find valid usernames — O365 `office` mode checks existence without authenticating, `oauth2` mode reads AADSTS error codes for MFA/lockout/disabled-account detail, OWA mode times NTLM responses, Teams searches people silently, SMTP tries VRFY/RCPT/EXPN — then spray or brute passwords with per-request delays and automatic stop-on-lockout so testing does not disable accounts. It also gathers candidate employee emails from LinkedIn (using your own session cookie) or search-engine dorks before validation. Reach for it when the target's exposed surface is mail/M365 and the job is disciplined, lockout-aware credential testing. It is not an on-premises Active Directory exploit tool: it authenticates over the wire and extracts nothing without valid credentials.

## Typical use cases

### Enumerate valid O365 users

```
gomapenum o365 userenum -u users.txt
```

Outcome: each address is classified as valid or invalid for the tenant; the default `office` mode makes no authentication attempt, while `-m oauth2` additionally surfaces AADSTS reasons (MFA required, account locked, disabled). `--thread` raises parallelism above the default 2.

### Password-spray O365 with lockout awareness

```
gomapenum o365 brute -u users.txt -p passwords.txt -s 10
```

Outcome: users are validity-checked first (`-c`, on by default), then each password is tried against the list with a 10-second pause between authentication requests (`-s`); working credentials print as found, and spraying halts automatically if an account locks (opt out with `--stopOnLockout=false`). Add `--no-bruteforce` to test user1/password1 line-aligned pairs instead of the full cross product.

### Enumerate OWA users by authentication timing

```
gomapenum owa userenum -u users.txt -t mail.example.com -o valid-users.txt
```

Outcome: the tool first measures the average NTLM response time for invalid users, then compares each candidate against that baseline — valid usernames answer measurably faster — and writes confirmed addresses to `valid-users.txt`. `--basic` switches the service to HTTP basic auth instead of NTLM.

### Enumerate SMTP recipients

```
gomapenum smtp userenum -u users.txt -d example.com -m VRFY
```

Outcome: resolves the domain's MX host when no `-t` target is given, then probes each username with the chosen method (`VRFY`, `RCPT`, `EXPN`, or all three by default) and reports which addresses the server accepts as deliverable.

### Harvest employee emails from LinkedIn

```
gomapenum linkedin gather -c "Example Corp" -f "{first}.{last}@example.com" -e -s AQEDAThA...
```

Outcome: lists the company's LinkedIn employees rendered as email addresses in the given format without visiting profiles (no notifications to targets); `-e` requires an exact company-name match and `-s` supplies your own `li_at` session cookie. Feed the output file into a `userenum` module to validate it.
