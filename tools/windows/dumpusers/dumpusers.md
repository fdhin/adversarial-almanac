---
name: "dumpusers"
categories:
  - blackarch-windows
version: "1.0"
homepage: "https://vidstromlabs.com/freetools/dumpusers/"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dumpusers

> Enumerates Windows account names and details even with RestrictAnonymous set to 1.

## What it's for

DumpUsers (Vidstrom Labs) walks a RID range against a remote Windows machine and lists every account it finds, working even when RestrictAnonymous=1 — the setting meant to hide the user list from anonymous enumeration (raising it to 2 blocks this tool). Per account it reports privilege level (administrator/user/guest), password age and expiry, lockout and disabled state, logon times, and group and operator memberships. It is a small standalone Windows console program, useful for demonstrating that anonymous account enumeration remains possible on a host; on Linux it runs under wine.

## Typical use cases

### Dump accounts from a domain controller verbosely

Enumerate users and their properties.

```
wine /usr/share/windows/dumpusers/dumpusers.exe -target dc01.example.com -type dc -start 500 -stop 2000 -mode verbose
```

Outcome: prints one entry per account whose RID falls in the given range (well-known RIDs such as 500 = Administrator come first), each with privilege level, password age, lockout and group details. If the enumeration endpoint is unavailable it reports "No service running at the target system".

### List account names only

Build a quick username list.

```
wine /usr/share/windows/dumpusers/dumpusers.exe -target 198.51.100.10 -type notdc -start 1000 -stop 3000 -mode quiet
```

Outcome: prints bare account names, one per line — suitable as input for username checks. Use `-type dc` for domain controllers and `-type notdc` for member machines, and widen the RID range if custom accounts extend beyond it.

*No corpus evidence — derived from upstream documentation.*
