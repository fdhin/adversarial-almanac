---
name: "ldeep"
categories:
  - blackarch-recon
version: "2.0.3.r15.gb57f12e"
homepage: "https://github.com/franc-pentest/ldeep"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "franc-pentest/ldeep"
github-stars: 612
github-pushed: "2026-11-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 79
gh-activity-checked: "2026-11-09"
---

# ldeep

> One-command LDAP/Active Directory enumeration: users, groups, GPOs, trusts, delegations, gMSA secrets, LAPS and more — plus an offline mode that replays its own dumps.

## What it's for

ldeep queries an Active Directory domain over LDAP and packages the dozens of lookups a pentester repeats per engagement behind one subcommand interface. The `ldap` mode covers reads (users, groups, machines, ou, gpo, trusts, delegations, gmsa with secret retrieval, laps, sccm, shadow_principals, subnets, zones, ...) and active changes (create_user, create_computer, modify_password, add_to_group, unlock). Authentication supports password, NT hash (`-H LMHASH:NTHASH`), Kerberos ticket (`-k`) or certificate; anonymous bind works too. The `cache` mode re-queries a previously saved dump from disk without touching the domain again, and `protections` audits security settings. Because it can modify directory objects, point it at systems you are authorized to change.

## Typical use cases

### Dump everything the domain will give you

```
ldeep ldap -u user -p 'Password1' -d corp -s ldap://dc01.corp.example.com all backup/corp
```

Outcome: walks every enumeration module in turn — printing `[+] Retrieving <module> output` progress lines for auth_policies, delegations, gpo, groups, machines, trusts, users and the rest — and stores each module's results under `backup/corp` so the whole domain snapshot can be queried offline later.

### List domain users (or any single object class)

```
ldeep ldap -u user -p 'Password1' -d corp -s ldap://dc01.corp.example.com users
```

Outcome: prints the domain's user accounts in a table; swap the subcommand (`groups`, `machines`, `gpo`, `trusts`, `delegations`, `laps`, `gmsa`, ...) for any other class. Authentication works the same with an NT hash via `-H 'LMHASH:NTHASH'` instead of `-p`.

### Replay a saved dump offline

```
ldeep cache -d backup -p corp users
```

Outcome: answers the same queries from the files saved by the earlier `all` run (`-d` directory, `-p` dump prefix) instead of the LDAP server — identical output, zero domain traffic, useful for revisiting data collected during a window of access.

*No corpus evidence — derived from upstream documentation (franc-pentest/ldeep README, verified against the master-branch source).*
