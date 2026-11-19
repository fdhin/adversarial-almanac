---
name: "goddi"
categories:
  - blackarch-recon
version: "1.2"
homepage: "https://github.com/NetSPI/goddi"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-04"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "NetSPI/goddi"
github-stars: 424
github-pushed: "2022-05-31"
github-archived: true
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# goddi

> "Go dump domain info" — one-shot authenticated dump of an Active Directory domain over LDAP into per-collection CSV files.

## What it's for

goddi connects to a domain controller with a single domain account and dumps a broad slice of the domain in one run: domain users (flagging description keywords like "pass"), privileged-group members (domain/enterprise/forest admins), locked and disabled accounts, computers, controllers, groups, OUs, sites and subnets, GPOs, trusts, the account policy, FSMO roles, SPNs (flagging those owned by domain admins), stale machine-account passwords, LAPS passwords, and — on Linux with mount/cifs-utils — GPP passwords from SYSVOL. Everything is written as CSVs into a `csv/` directory in the working directory. The project is archived and no longer maintained; it defaults to LDAPS over 636, with StartTLS and plaintext as options. It is a collection tool — read-only against the directory (the GPP check mounts SYSVOL read-only).

## Typical use cases

### Dump a domain over LDAPS

```
goddi -username=user -password=Password1 -domain=example.com -dc=dc.example.com
```

Outcome: binds over TLS on port 636, walks every collection, prints progress per section (users found, groups found, SPNs, GPP passwords, ...), and writes each collection to its own CSV file under `./csv/` — an immediate post-credential domain overview. On Linux run it with sudo (needed for the SYSVOL mount).

### Dump over plaintext or StartTLS

```
goddi -username=user -password=Password1 -domain=example.com -dc=dc.example.com -unsafe
```

Outcome: same full dump but over a cleartext LDAP connection on 389 (use `-startTLS` instead to upgrade port 389 to TLS) — for labs or captures where the DC's certificate setup breaks LDAPS.

*No corpus evidence — derived from upstream documentation.*
