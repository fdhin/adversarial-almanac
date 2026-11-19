---
name: "bkhive"
categories:
  - blackarch-cracker
version: "1.1.1"
homepage: "http://sourceforge.net/projects/ophcrack"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-24"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "aur-archive/bkhive"
github-stars: 0
github-pushed: "2015-08-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bkhive

> Dumps the syskey bootkey from a Windows NT/2000/XP/Vista SYSTEM hive.

## What it's for

When Windows syskey mode is active, the password hashes in the SAM database are encrypted with a bootkey derived from values scattered and obfuscated inside the SYSTEM hive. bkhive extracts that key from an offline hive file — the first half of offline hash dumping; the key is then fed to samdump2 to decrypt the SAM into crackable LM/NT hashes. It works against the default syskey mode where the key is stored on the local system; when the key was set to a startup passphrase or stored on a floppy, there is nothing to extract and the syskeyed hashes must be cracked as they are. It is a companion of the ophcrack project.

## Typical use cases

### Extract the bootkey from a mounted SYSTEM hive

```
bkhive /mnt/ntfs/WINDOWS/system32/config/system bootkey
```

Outcome: reads the hive, walks the key-derivation steps, and writes the recovered 16-byte bootkey to the file bootkey; pass that file to samdump2 (`samdump2 SAM bootkey`) to dump the password hashes.

*No corpus evidence — derived from upstream documentation.*
