---
name: "ldapwordlistharvester"
categories:
  - blackarch-wordlist
version: "1.2.r20.g78cd116"
homepage: "https://github.com/p0dalirius/pyLDAPWordlistHarvester"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-28"
build-system: scripts
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "p0dalirius/pyLDAPWordlistHarvester"
github-stars: 379
github-pushed: "2026-03-24"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# ldapwordlistharvester

> Generate a domain-specific password wordlist from the naming and metadata already present in an Active Directory domain's LDAP tree.

## What it's for

Binds to a domain controller over LDAP with any valid domain credentials and harvests words from users' `name` and `sAMAccountName`, computer and group names, organizational units, AD site names and object descriptions into a single wordlist. The premise: domain users' passwords are usually built from words present in the domain itself (company, project and people names), so a list generated from the target's own LDAP outperforms generic lists when cracking NTDS/NTLM hashes — the bigger the domain, the better the list. The BlackArch package wraps the upstream `LDAPWordlistHarvester.py` as the command `ldapwordlistharvester` (a PowerShell edition also exists upstream for Windows-side runs).

## Typical use cases

### Harvest a wordlist with password authentication

```
ldapwordlistharvester -d corp.example.com -u user -p 'Password1' \
    --dc-ip 198.51.100.10 -o corp-words.txt
```

Outcome: authenticates to the domain controller, walks the LDAP tree and writes one word per line to `corp-words.txt` (the path given to `--outputfile`), printing the words as they are harvested — then feed it to hashcat against NTDS hashes with a rules file and `--loopback` so cracked outputs feed later guesses.

### Harvest with a hash or over LDAPS

```
ldapwordlistharvester -d corp.example.com -u user -H :nth-hash --ldaps --dc-ip 198.51.100.10
```

Outcome: same harvest authenticating with an NT hash instead of a password (`-H` takes `[lmhash:]nthash`) and using LDAPS instead of cleartext LDAP — useful when only a hash is available or the DC requires TLS.

*No corpus evidence — derived from upstream documentation.*
