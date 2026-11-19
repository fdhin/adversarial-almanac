---
name: "extractbitlockerkeys"
categories:
  - blackarch-windows
version: "1.2.r20.g54730e4"
homepage: "https://github.com/p0dalirius/extractbitlockerkeys"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-21"
build-system: windows
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "p0dalirius/extractbitlockerkeys"
github-stars: 405
github-pushed: "2026-01-20"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# extractbitlockerkeys

> Dumps every BitLocker recovery key escrowed in an Active Directory domain via LDAP.

## What it's for

ExtractBitlockerKeys, by Podalirius (Rémi Gascou), is a system-administration and post-exploitation script that automatically extracts the BitLocker recovery keys of all computers in a Windows domain: it authenticates to a domain controller, iterates over LDAP result pages to reach every computer object no matter the domain's size, and multithreadedly pulls each machine's recovery key (`msFVE-RecoveryPassword`) along with its volume GUID, creation date and OU placement. The result is a complete offline backup of the domain's recovery keys — useful for administrators auditing that key escrow is actually populated, and equally useful to an attacker with even a low-privileged domain account, since read access to this data is often the default. It only reads keys from Active Directory; it does not attack BitLocker encryption itself, and a companion PowerShell version (`ExtractBitlockerKeys.ps1`) does the same from Windows hosts.

## Typical use cases

### Dump all recovery keys in the domain

```
extractbitlockerkeys -d example.com -u user -p Password1 --dc-ip 198.51.100.10
```

Outcome: as LDAP pages are processed, results stream to the console — one entry per key with computer FQDN, domain, recovery key, volume GUID, creation date and OUs.

### Export the keys to a report file

```
extractbitlockerkeys -d example.com -u user -p Password1 --dc-ip 198.51.100.10 --export-xlsx bitlocker-keys.xlsx --export-json bitlocker-keys.json
```

Outcome: in addition to the console output, `bitlocker-keys.xlsx` and `bitlocker-keys.json` are written to the working directory with the full key inventory (`--export-sqlite` writes a SQLite3 database the same way), ready to hand over or archive offline.

### Use Kerberos or a hash instead of a password

```
KRB5CCNAME=user.ccache extractbitlockerkeys -d example.com -u user -k --no-pass --dc-ip 198.51.100.10
```

Outcome: authentication is taken from the exported ticket cache (`-k` with `--no-pass` suppresses the password prompt); `-H <lm>:<nt>` is the pass-the-hash alternative for a captured NT hash.

*No corpus evidence — derived from upstream documentation.*
