---
name: "samdump2"
categories:
  - blackarch-cracker
version: "3.0.0"
homepage: "http://sourceforge.net/projects/ophcrack/files/samdump2/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2015-09-06"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "foreni-packages/Samdump2"
github-stars: 4
github-pushed: "2015-09-25"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# samdump2

> Extracts Windows account password hashes from offline SAM and SYSTEM registry hives.

## What it's for

samdump2 (original author Nicola Cuomo, distributed by Objectif Securite
alongside ophcrack) turns a copied pair of Windows registry hives — SYSTEM
and SAM — into pwdump-format hash lines. It derives the syskey bootkey from
the SYSTEM hive itself, decrypts the per-user hashes, and prints
`user:rid:LM:NT` for each local account (disabled accounts are prefixed
`*disabled*`), ready for offline cracking with John the Ripper or hashcat.
Since version 3.0 the previously separate bkhive step is built in. It
targets Windows NT/2000/XP/Vista hives from `%WINDIR%\system32\config`;
it does not handle the protections used on modern Windows releases.

## Typical use cases

### Dump hashes from copied hive files

Point it at the SYSTEM hive first, then the SAM hive.

```
samdump2 system sam
```

Outcome: prints one pwdump-style `user:rid:LM:NT` line per local account to
stdout; feed the lines to a password cracker to test the accounts.

### Save the dump to a file for later cracking

Use `-o` to write the hash list to a file instead of stdout.

```
samdump2 -o hashes.txt /mnt/win/Windows/system32/config/SYSTEM /mnt/win/Windows/system32/config/SAM
```

Outcome: writes the same hash lines to `hashes.txt` — e.g. after mounting
a Windows partition from another OS — ready for `john hashes.txt` or
`hashcat -m 1000 hashes.txt`.
