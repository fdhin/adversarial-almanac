---
name: "ntdsxtract"
categories:
  - blackarch-forensic
version: "34.7fa1c8c"
homepage: "https://github.com/csababarta/ntdsxtract"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "csababarta/ntdsxtract"
github-stars: 336
github-pushed: "2022-03-24"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ntdsxtract

> Pull users, groups, computers — and with the SYSTEM hive, password hashes and credential material — out of a seized Active Directory NTDS.dit database.

## What it's for

ntdsxtract (Csaba Barta) is an Active Directory forensic framework that extracts objects from the NTDS.dit database of a domain controller. It consumes the `datatable` and `linktable` files produced by running esedbexport (from libesedb) against the dit, so the standard chain is: acquire `ntds.dit` plus the registry `SYSTEM` hive, run `esedbexport ntds.dit`, then point ntdsxtract at the exported tables. One script per object class — `dsusers.py` (users), `dscomputers.py` (computers), plus group, deleted-object, file-information, keytab and timeline modules. With the SYSTEM hive supplied it derives the decryption keys and recovers password hashes, password history and supplemental credentials (cleartext passwords where reversible encryption is enabled, Kerberos keys), exporting hashes in John the Ripper, oclHashcat or OphCrack formats. The module set targets the Windows Server 2003/2008-era database layout.

## Typical use cases

### Inventory the user objects

First pass over the exported tables.

```
dsusers.py datatable link_table work --csvoutfile users.csv
```

Outcome: every user object decoded and written one row per account to `work/users.csv` (attributes, UAC flags, ancestry); the same records also stream to stdout — the census of accounts held in the database.

### Recover password hashes for offline cracking

The SYSTEM hive supplies the bootkey; hashes export in cracker-ready formats.

```
dsusers.py datatable link_table work --syshive SYSTEM --passwordhashes --pwdformat john --ntoutfile nthashes.txt --lmoutfile lmhashes.txt
```

Outcome: LM and NT hashes written in John the Ripper format to the named files, ready for [[john]]; `--pwdformat ocl` or `ophc` switch the output to oclHashcat/OphCrack formats.

### Examine password history and supplemental credentials

What the accounts used previously, and stored plaintext material.

```
dsusers.py datatable link_table work --syshive SYSTEM --passwordhistory --supplcreds
```

Outcome: per-user password history plus supplemental credentials — cleartext passwords for accounts with reversible encryption enabled and Kerberos keys — printed per user object.

### Target a single account

Select by regular expression or identifier.

```
dsusers.py datatable link_table work --name 'admin.*' --membership
```

Outcome: only accounts whose name matches the regexp are listed, together with the groups each belongs to; `--sid <sid>` or `--guid <guid>` select by identifier instead, and `--active`/`--locked`/`--uac <hex>` filter by account state.

### Extract computer objects

Machine accounts and their hashes.

```
dscomputers.py datatable work --syshive SYSTEM --passwordhashes
```

Outcome: computer objects decoded with their machine-account password hashes, using the same output conventions as the user module.

*No corpus evidence — derived from upstream documentation.*
