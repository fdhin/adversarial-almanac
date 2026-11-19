---
name: "impacket-lookupsid"
categories:
  - corpus-windows
homepage: "https://github.com/fortra/impacket"
origin: "corpus"
github-repo: "fortra/impacket"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# impacket-lookupsid

> Brute-force SID lookups over DCE/RPC (MS-LSAT) to enumerate domain users, groups and machines.

## What it's for

lookupsid.py walks the relative-identifier (RID) space of a domain — 0 to 4000 by default, extendable with an optional second positional argument — and asks the LSAT service to translate each SID into a name. Every hit is printed as `RID: DOMAIN\name (SidType)`, yielding a full user/group/machine inventory without touching LDAP. It accepts real credentials, NT hashes (`-hashes`), or no authentication at all when the target permits null sessions (`-no-pass`), which makes it a classic first probe after finding guest or anonymous SMB access. It is read-only enumeration and grants nothing by itself.

## Typical use cases

### Enumerate users through a null session

No credentials at all — guest or anonymous against a host that allows it.

```
impacket-lookupsid 'example.com/guest'@198.51.100.10 -no-pass
```

Outcome: prints every resolvable RID as `1105: EXAMPLE\svc_helpdesk (SidTypeUser)` style lines, ending with a total SID count; a full listing instead of an access-denied means null-session LSAT is open and the domain user list is free for the taking.

### Enumerate with valid credentials

Use a known account for the same RID walk.

```
impacket-lookupsid example.com/user:Password1@198.51.100.10
```

Outcome: dumps users, groups and machine accounts (`SidTypeUser`, `SidTypeGroup`, `SidTypeAlias`…) up to RID 4000; append a second positional to raise the ceiling, e.g. `… 10000`.

### Save a clean username list

Filter the output down to bare usernames for use as a wordlist.

```
impacket-lookupsid 'example.com/guest'@198.51.100.10 -no-pass | grep 'SidTypeUser' | sed 's/.*\\\(.*\) (SidTypeUser)/\1/' > users.txt
```

Outcome: users.txt contains one username per line with the `DOMAIN\` prefix stripped, ready to feed into password sprayers or brute-forcers.
