---
name: "msmailprobe"
categories:
  - blackarch-scanner
version: "1.c01c8bf"
homepage: "https://github.com/busterb/msmailprobe"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-19"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "busterb/msmailprobe"
github-stars: 205
github-pushed: "2019-05-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# msmailprobe

> Enumerate valid users on Exchange and Office 365 via error-based (O365) and timing-based (on-prem) oracles in default mail services.

## What it's for

msmailprobe (Go) profiles and enumerates mail infrastructure tied to a domain. Its `identify` command maps a host for Exchange/O365 integration — O365-related DNS records, the internal AD domain name recovered from NTLMSSP responses, which on-prem services are vulnerable to time-based enumeration, and which are password-sprayable. Its `userenum` command then validates usernames: error-based against Office 365, or timing-based against on-prem Exchange services (OWA and friends), where valid users take measurably longer to fail authentication. It enumerates users only — it never attempts password guesses. An `examples` subcommand prints the tool's own current syntax cheat sheet.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Identify the mail services of a host

Map what a mail host exposes before enumerating users.

```
msmailprobe identify -t mail.example.com
```

Outcome: prints the O365-related DNS records, the extracted internal domain name where recoverable, the on-prem services vulnerable to time-based user enumeration, and the password-sprayable services found on the host.

### Enumerate users against Office 365

Validate an email list using O365's error-based oracle.

```
msmailprobe userenum --o365 -E emails.txt -o valid.txt --threads 25
```

Outcome: every address in the list is probed and the valid ones are written to valid.txt; a single address can be checked with `-e user@example.com` instead of `-E`.

### Enumerate users against on-prem Exchange

Validate usernames through response-timing differences.

```
msmailprobe userenum --onprem -t mail.example.com -U users.txt -o valid.txt --threads 25
```

Outcome: existing usernames are distinguished by slower authentication failures across the enumerated Exchange services and written to valid.txt; a single username can be checked with `-u user`.

### Show the built-in syntax cheat sheet

Get the tool's own current examples for every subcommand.

```
msmailprobe examples
```

Outcome: prints the author-maintained example invocations for `identify` and `userenum`, useful when this note's flags drift from the installed version.
