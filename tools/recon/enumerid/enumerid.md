---
name: "enumerid"
categories:
  - blackarch-recon
version: "37.ba5540f"
homepage: "https://github.com/Gilks/enumerid"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-21"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "Gilks/enumerid"
github-stars: 32
github-pushed: "2026-01-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# enumerid

> Pure-Python RPC enumeration of well-known Active Directory RIDs: domain computers, admins, users and password policy, with optional DNS resolution of hostnames.

## What it's for

enumerid (Gilks) connects to a domain controller over RPC and enumerates members of arbitrary RIDs — the well-known relative identifiers such as 512 (Domain Admins), 515 (Domain Computers) and 516 (Domain Controllers). Unlike classic RID-cycling tools it targets named groups directly (`-s` accepts a string like "Domain Admins" when you don't know the RID), can resolve each member hostname to its leased IP (`-d`) to map live hosts, and reproduces `net user /domain`-style output plus the domain password policy without needing Windows tooling. It was written to orient yourself in a network after the initial foothold; it performs read-only LDAP/RPC queries and does not authenticate beyond the credentials you pass it on the command line (anonymous works against permissive servers).

## Typical use cases

### Map domain computers with DNS resolution

Enumerate the Domain Computers group and resolve member hostnames to IPs.

```
./enumerid domain/user:Password1@198.51.100.10 -r 515 -d
```

Outcome: lists every member of RID 515 (Domain Computers) and resolves each hostname to its leased IP address, giving an internal host inventory in one shot.

### List domain admins

Enumerate the Domain Admins group by RID.

```
./enumerid domain/user:Password1@198.51.100.10 -r 512
```

Outcome: prints the members of RID 512 (Domain Admins) — the highest-value account names for follow-up targeting. Swap in `-r 516` for Domain Controllers.

### Enumerate a group by name

Skip RID knowledge entirely.

```
./enumerid domain/user:Password1@198.51.100.10 -s "Domain Admins"
```

Outcome: looks up the named group's RID via RPC and enumerates its members; use `-h` to display commonly used RIDs when unsure.

### Dump the domain password policy

Recover password rules as `net user /domain` would show them.

```
./enumerid domain/user:Password1@198.51.100.10 -p
```

Outcome: prints the domain password policy (complexity, length, history, lockout) — input for cracking math and spraying decisions.

### Anonymous enumeration against a permissive host

Try RID enumeration with a null session.

```
./enumerid domain/'':''@198.51.100.10 -r 515 -d
```

Outcome: if the server allows anonymous RPC, returns the Domain Computers list and IP resolutions without any credentials. `-u` enumerates all domain users with descriptions, `-g` all group names with RIDs.

No corpus evidence — derived from upstream documentation.
