---
name: "spray365"
categories:
  - blackarch-cracker
version: "0.2.2.beta.r1.g22b39af"
homepage: "https://github.com/MarkoH17/Spray365"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-19"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "MarkoH17/Spray365"
github-stars: 382
github-pushed: "2025-06-24"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spray365

> Two-step Office 365 / Azure AD password sprayer built around pre-generated, resumable "execution plans".

*No corpus evidence — derived from upstream documentation.*

## What it's for

Identifying valid credentials for Microsoft accounts (Office 365 / Azure AD)
while dodging the protections around them. Spray365 splits every operation
into two steps: first generate an execution plan — a JSON file describing
exactly which user/password attempts will be made — then execute that plan.
The plan decouples planning from spraying, lets an interrupted spray resume
(`-R`), and enables a shuffle mode (`-S`/`--shuffle_auth_order`) that
randomizes attempt order to complicate Azure Smart Lockout and detection.
Random User-Agents (default on) help expose conditional access policies that
trust specific device types, and an HTTP(S) proxy option routes attempts
through tools like Burp Suite. A separate audit mode generates plans that
attempt all User-Agent/client-ID/endpoint combinations for a known-valid
credential pair to map MFA and conditional access gaps. A `review` command
summarizes results: valid accounts, valid credentials, and "partial" ones
that authenticated but were blocked by MFA or policy. The upstream repository
is archived and no longer maintained.

## Typical use cases

### Generate a standard execution plan

Describe the spray before running it.

```
python3 spray365.py generate normal -ep plan.s365 -d example.com -u usernames.txt -pf passwords.txt
```

Outcome: writes `plan.s365`, the JSON execution plan pairing every user in
`usernames.txt` with every password in `passwords.txt` against the
`example.com` tenant, honoring the default 30-second `--delay` between
attempts; a single password can be given with `-p Password1` instead of a
file.

### Execute the plan

Run the prepared spray, throttled and resumable.

```
python3 spray365.py spray -ep plan.s365
```

Outcome: processes the plan against Microsoft's authentication endpoints and
writes a timestamped JSON results file; it aborts after 5 observed lockouts
(raise/lower with `-l`, disable with `-l 0`), skips users already found valid
unless `-i` is set, and after an interruption `-R <index>` resumes from a
position in the plan.

### Audit MFA and conditional access with known credentials

Map which authentication paths a valid account can use.

```
python3 spray365.py generate audit -ep audit.s365 -d example.com -u valid_creds.txt --passwords_in_userfile
python3 spray365.py spray -ep audit.s365
```

Outcome: with `--passwords_in_userfile` each `user:password` line of
`valid_creds.txt` becomes a known-good credential; the audit plan then tries
every User-Agent / AAD client-ID / endpoint-ID combination for those pairs,
revealing device-trust or MFA configurations that still grant access.

### Review spray results

Turn the results JSON into conclusions.

```
python3 spray365.py review spray365_results.json
```

Outcome: prints valid accounts, valid credentials and partially valid ones
(authentication succeeded but MFA/conditional access blocked access), with
`--show_invalid_creds` / `--show_invalid_users` adding the failed attempts;
the underlying JSON remains available for processing with jq or other tools.
