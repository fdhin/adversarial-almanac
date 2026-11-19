---
name: "go-windapsearch"
categories:
  - blackarch-recon
version: "v0.3.0.r22.ged05587"
homepage: "https://github.com/ropnop/go-windapsearch"
packaged-by: [blackarch]
blackarch-builddate: "2023-06-27"
build-system: go
license: BSD
meta-checked: "2026-11-09"
github-repo: "ropnop/go-windapsearch"
github-stars: 401
github-pushed: "2021-03-25"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# go-windapsearch

> Go tool for Active Directory enumeration over LDAP — module-based queries for users, groups, computers, privileged accounts, and delegation, with anonymous, password, or pass-the-hash authentication.

## What it's for

windapsearch (the Go rewrite of ropnop's earlier Python tool) wraps common LDAP enumeration of a Windows domain into named modules: `users`, `computers`, `groups`, `members`, `domain-admins`, `privileged-users`, `user-spns` (kerberoasting targets), `unconstrained`, `admin-objects`, `gpos`, `dns-zones`, `dns-names`, `metadata`, `search`, and `custom`. It can bind anonymously (null sessions, where allowed), with username/password, with a full bind DN, or with an NTLM hash for pass-the-hash; it speaks LDAP, LDAPS (`--secure`, cert not verified), or StartTLS, and can tunnel through a SOCKS5 proxy. Output mimics `ldapsearch` text, or converts to JSON with binary attributes (SIDs, GUIDs, timestamps, UAC flags) translated to human-readable form. It enumerates only — it performs no exploitation.

## Typical use cases

### Fingerprint a domain controller anonymously

```
windapsearch -d example.com --dc dc.example.com -m metadata
```

Outcome: with no credentials (anonymous bind), prints LDAP server metadata such as the root DSE details — a low-noise first probe to confirm LDAP reachability and null-session policy before authenticating.

### Enumerate domain users

```
windapsearch -d example.com -u user@example.com -m users
```

Outcome: prompts for the password, binds, and lists all user objects with default attributes (`cn`, `sAMAccountName`); per-module `-h` shows module options like `--attrs` to select attributes, `-s` to filter, and `--filter` for extra LDAP syntax.

### Find kerberoasting and privileged targets

```
windapsearch -d example.com -u user@example.com -m privileged-users
```

Outcome: recursively lists members of all highly privileged groups (domain/enterprise/schema admins); swap the module for `user-spns` to get every user with a Service Principal Name — the input list for kerberoasting. `-m domain-admins` does the same recursively for Domain Admins specifically.

### Machine-readable output for tooling

```
windapsearch -d example.com -u user@example.com -m computers -j -o computers.json
```

Outcome: emits a JSON array where binary LDAP values are decoded (objectSid to SDDL SID string, timestamps to ISO 8601, userAccountControl to flag names), and saves it to `computers.json` — directly consumable by downstream scripts. Pass-the-hash works via `--hash <LM>:<NT>` with `--ntlm`.

*No corpus evidence — derived from upstream documentation.*
