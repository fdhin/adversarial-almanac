---
name: "creddump"
categories:
  - blackarch-cracker
version: "3.ed95e1a"
homepage: "https://github.com/moyix/creddump"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "moyix/creddump"
github-stars: 290
github-pushed: "2019-05-08"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# creddump

> Offline extraction of Windows credential material — SAM password hashes, cached domain passwords and LSA secrets — from registry hive files.

## What it's for

Parses Windows registry hives copied off a host and pulls secrets out of them with no dependence on Windows APIs: LM and NT hashes (SYSKEY-protected) from the SAM hive, cached domain logon credentials, and LSA secrets. It reproduces what bkhive/samdump2, cachedump and lsadump2 each did, in one platform-independent Python package that reads hive files directly — only the hive files and pycrypto are needed. On BlackArch the three scripts are exposed as `pwdump`, `cachedump` and `lsadump`.

## Typical use cases

### Dump local account hashes

Recover SAM password hashes offline.

```
pwdump SYSTEM SAM
```

Outcome: derives the SYSKEY from the SYSTEM hive, decrypts the SAM, and prints pwdump-format `user:RID:LM:NT` lines for each local account, ready for offline cracking.

### Dump cached domain credentials

Enumerate domain accounts that have logged on to this machine.

```
cachedump SYSTEM SECURITY
```

Outcome: computes the cached-credential key from the SYSTEM and SECURITY hives and prints the cached domain password hashes (domain cached credentials, DCC) with their usernames.

### Dump LSA secrets

Read service passwords and other protected blobs.

```
lsadump SYSTEM SECURITY
```

Outcome: derives the LSA key and decrypts the LSA secrets store, printing its contents — commonly plaintext service-account passwords, DPAPI keys and autologon credentials.

*No corpus evidence — derived from upstream documentation.*
