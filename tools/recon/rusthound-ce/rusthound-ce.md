---
name: "rusthound-ce"
categories:
  - blackarch-recon
version: "v2.5.28.r3.g36e619a"
homepage: "https://github.com/g0h4n/RustHound-CE"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "g0h4n/RustHound-CE"
github-stars: 632
github-pushed: "2026-11-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 235
gh-activity-checked: "2026-11-09"
---

# rusthound-ce

> Cross-platform BloodHound Community Edition collector written in Rust — LDAP(S) or ADWS in, BloodHound-ready JSON out.

## What it's for

RustHound-CE collects Active Directory objects (users, groups, computers, OUs, GPOs, containers) over LDAP(S) or ADWS and writes the JSON files that BloodHound Community Edition ingests, optionally zipped into a single archive. Reach for it where SharpHound is a poor fit: a single static Rust binary that runs on Linux, Windows and macOS collects from a compromised non-Windows host without .NET. It is a collector only — graphing and path analysis happen in BloodHound itself. It targets BloodHound CE exclusively; for legacy BloodHound 4.x output use the sibling [[rusthound]] (same author lineage, same flag family).

## Typical use cases

### Collect a domain into one ingest archive

Authenticate with a domain account and produce the zip BloodHound CE expects.

```
rusthound-ce -d CORP.LOCAL -u user@CORP.LOCAL -z
```

Outcome: locates a domain controller, collects over LDAP, and writes the BloodHound CE JSON files plus a zip archive ready to drag into the BloodHound UI.

### Point at a specific DC over LDAPS with the full collection set

```
rusthound-ce -d CORP.LOCAL -f DC01.CORP.LOCAL -u user@CORP.LOCAL -p 'Password1' --ldaps -o /tmp/bh -c All -z
```

Outcome: authenticates over LDAPS (636) to the named domain controller, runs every collector module, and lands the JSON/zip in /tmp/bh instead of the current directory.

### Authenticate with a certificate instead of a password

```
rusthound-ce -d CORP.LOCAL -f DC01.CORP.LOCAL --crt user.crt --key user.key --ldaps -o /tmp/bh -c All -z
```

Outcome: binds with the PEM client certificate and collects as above; a PFX bundle works via --pfx (with --pfx-pass when it is password-protected), useful when you hold PKI material but no password.

### Survive large or flaky domains with caching

```
rusthound-ce -c All -d CORP.LOCAL -u user -p 'Password1' -o /tmp/bh -z --cache
rusthound-ce -d CORP.LOCAL -o /tmp/bh -z --resume
```

Outcome: the first run spills LDAP data to .rusthound-cache/ instead of holding it in memory; the second picks the collection back up from the saved state after an interruption.

*No corpus evidence — derived from upstream documentation.*
