---
name: "ridenum"
categories:
  - blackarch-cracker
version: "75.9e3b89b"
homepage: "https://github.com/trustedsec/ridenum"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "trustedsec/ridenum"
github-stars: 317
github-pushed: "2024-07-28"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ridenum

> Null-session RID cycling to enumerate — and then brute-force — domain accounts via rpcclient.

## What it's for

Enumerates user accounts on a Windows domain controller (or any host permitting null sessions) by RID cycling: it resolves the base domain SID via lsaquery, then walks SIDs from a start RID to an end RID with rpcclient lookupsids and reports every existing `DOMAIN\user` account — no credentials required where null sessions are allowed. After enumerating, it can brute-force each discovered account by driving rpcclient logons through python-pexpect. Positional arguments after the RID range are interpreted by type: a value naming an existing file is treated as a wordlist, otherwise as a username or password; supplying both a password file and a pre-dumped username file (one `DOMAIN\user` per line) skips enumeration and goes straight to guessing. It needs rpcclient (Samba suite) on PATH. If null sessions are blocked, pass one valid username and password instead and the sweep runs authenticated.

## Typical use cases

### Enumerate accounts over a null session

Walk the RID range with no credentials at all.

```
ridenum 198.51.100.10 500 50000
```

Outcome: resolves the domain SID, cycles RIDs 500-50000, prints each discovered `Account name: DOMAIN\user` line, and saves the full list to `198.51.100.10_users.txt` in the current directory.

### Enumerate, then brute-force the found accounts

Chain enumeration and password guessing in one run.

```
ridenum 198.51.100.10 500 50000 passwords.txt
```

Outcome: enumerates accounts as above into `198.51.100.10_users.txt`, then attempts an rpcclient logon for each account with every password in `passwords.txt`, printing each valid user/password pair it finds.

### Brute-force a known user list

Skip RID cycling when accounts are already dumped.

```
ridenum 198.51.100.10 500 50000 passwords.txt users.txt
```

Outcome: reads `DOMAIN\user` lines from `users.txt` and tries every password in `passwords.txt` against each, reporting working credentials without re-running the enumeration phase.

### Enumerate with valid credentials

Use one known account where null sessions are closed.

```
ridenum 198.51.100.10 500 50000 user Password1
```

Outcome: runs the RID sweep authenticated as `user` (password `Password1`) instead of via a null session, which works against hosts that reject anonymous logons.

*No corpus evidence — derived from upstream documentation.*
