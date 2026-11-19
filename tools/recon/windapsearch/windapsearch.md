---
name: "windapsearch"
categories:
  - blackarch-recon
version: "28.7724ec4"
homepage: "https://github.com/ropnop/windapsearch"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ropnop/windapsearch"
github-stars: 994
github-pushed: "2022-04-20"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# windapsearch

> Python LDAP enumerator for Windows domains: users, groups, computers, delegations and ACL-protected objects, with optional anonymous binds.

## What it's for

windapsearch (ropnop) talks LDAP directly to a domain controller and turns the directory into enumerated lists — all users (`-U`), groups (`-G`), computers (`-C`), group members, privileged accounts, SPN-bearing users (kerberoasting candidates), unconstrained-delegation objects and GPOs. It shines at the first-contact stage: with no credentials it will still attempt an anonymous bind and read the default naming context, which on permissive domains already leaks the user list; with any valid domain account (`-u user@corp.example.com -p Password1`) every query unlocks. Results can be narrowed with `--attrs`/`--full`, arbitrary LDAP filters via `--custom`, and saved as TSV with `-o`. It does not authenticate with hashes or tickets (no pass-the-hash) and performs no attacks — it is read-only enumeration, feeding tools like GetNPUsers.py or a password spray.

Commands below are verified against the upstream README (`-h` output).

## Typical use cases

### Anonymous user enumeration without credentials

Check whether the DC's LDAP permits null binds before you have any account.

```
./windapsearch.py -d corp.example.com --dc-ip 198.51.100.10 -U
```

Outcome: attempts an anonymous LDAP bind to `198.51.100.10`; if allowed, prints every `objectCategory=user` entry (commonName plus userPrincipalName). If the bind is refused, the default naming context may still print — that itself is a finding about LDAP hardening.

### Authenticated full-attribute user dump

Pull everything stored on user objects for offline review.

```
./windapsearch.py -d corp.example.com --dc-ip 198.51.100.10 -u 'user@corp.example.com' -p 'Password1' -U --full > users.txt
```

Outcome: dumps all LDAP attributes for every user (descriptions, last-logon details, servicePrincipalNames) to `users.txt` — grep it for passwords left in description fields. Use `--attrs mail,telephoneNumber` instead of `--full` when you only need specific attributes.

### Group membership of a specific group

See who belongs to an interesting group (remote management, admins, ...).

```
./windapsearch.py -d corp.example.com --dc-ip 198.51.100.10 -u 'user@corp.example.com' -p 'Password1' -m "remote management users"
```

Outcome: fuzzy-matches the group name, resolves its full DN and lists all members. `--da` is a shortcut for the same query against "Domain Admins" with recursive nested-membership lookups.

### Find privileged and protected objects

Enumerate accounts worth targeting for privilege escalation.

```
./windapsearch.py -d corp.example.com --dc-ip 198.51.100.10 -u 'user@corp.example.com' -p 'Password1' --admin-objects --user-spns
```

Outcome: prints all objects with protected ACLs (the de-facto admin accounts) and, separately, every user carrying a servicePrincipalName — the kerberoasting candidate list to feed into a ticket-request tool.

### Raw custom LDAP filter

Ask a question the built-in switches do not cover.

```
./windapsearch.py -d corp.example.com --dc-ip 198.51.100.10 -u 'user@corp.example.com' -p 'Password1' --custom "objectClass=*"
```

Outcome: runs the supplied filter verbatim against the domain naming context and prints matching entries; any valid LDAP filter syntax works. Add `-o outdir/` to any invocation to also write results as TSV files.
