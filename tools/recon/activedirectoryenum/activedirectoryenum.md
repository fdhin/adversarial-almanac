---
name: "activedirectoryenum"
categories:
  - blackarch-recon
version: "0.5.0"
homepage: "https://github.com/CasperGN/ActiveDirectoryEnumeration"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "CasperGN/ActiveDirectoryEnumeration"
github-stars: 147
github-pushed: "2026-03-17"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# activedirectoryenum

> Python LDAP wrapper (ADE) that enumerates an Active Directory domain and chains the common post-enum checks: ASREProast, Kerberoast, SYSVOL cpasswords, and BloodHound output.

## What it's for

ActiveDirectoryEnum (ADE) is a Python tool that connects to a domain controller over LDAP or LDAPS, enumerates domain objects, and bundles the follow-on attacks an assessor usually runs next: finding accounts without Kerberos pre-authentication, harvesting SPNs for Kerberoasting, searching SYSVOL GPOs for encrypted `cpassword` values (and decrypting them), and dumping the domain in BloodHound-ingestible JSON. It runs with any standard domain account (or with no credentials at all, gathering what an anonymous session can) and flags likely-exploitable conditions it notices along the way, with PoC exploit references for issues such as CVE-2020-1472. It is a Windows-domain enumeration tool; it does not touch non-LDAP services beyond optional SMB share checks.

## Typical use cases

### Enumerate a domain over LDAPS as a domain user

Run the baseline collection against a domain controller.

```
python -m ade --dc dc01.example.com -u user@example.com -s
```

Outcome: prompts for the account password, connects to the DC over LDAPS, and prints enumerated users, groups, computers, and shares; findings include accounts flagged as exploitable, e.g. `DC may be vulnerable to: cve-2020-1472`.

### Start without credentials

See what an anonymous LDAP session reveals before you have any account.

```
python -m ade --dc dc01.example.com --no-creds
```

Outcome: ADE connects without authentication and gathers whatever the environment's anonymous LDAP policy still exposes, giving a starting point for credential attacks.

### Hunt accounts without Kerberos pre-authentication

```
python -m ade --dc dc01.example.com -u user@example.com -kp
```

Outcome: lists users for which Kerberos pre-authentication is not required — accounts whose AS-REP hashes can be cracked offline without any domain credentials beyond the query account.

### Kerberoast all SPNs

Harvest service tickets for offline cracking.

```
python -m ade --dc dc01.example.com -u user@example.com -spn
```

Outcome: requests tickets for every SPN-bearing account and outputs them in crackable form; run a cracker against the captured hashes to target service account passwords.

### Search SYSVOL for cpassword-encrypted GPO credentials

```
python -m ade --dc dc01.example.com -u user@example.com -sysvol
```

Outcome: downloads GPO files from SYSVOL, locates `cpassword` entries, and decrypts them to plaintext — a classic way to recover stale service-account passwords stored in Group Policy preferences.

### Dump the domain for BloodHound

```
python -m ade --dc dc01.example.com -u user@example.com -bh
```

Outcome: writes JSON collections in the format BloodHound expects, ready to ingest and analyze for attack paths. `--all` runs every check (including all of the above) in one pass.

### Look up PoC exploit code for a flagged CVE

```
python -m ade --exploit cve-2020-1472
```

Outcome: prints a link to public PoC exploit code for the named CVE, e.g. the CVE-2020-1472 researcher PoC repository — a shortcut from a finding to a working exploit path.

*No corpus evidence — derived from upstream documentation.*
