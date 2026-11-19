---
name: "dpapick"
gentoo-category: "app-forensics"
version: "0.3"
homepage: "http://www.dpapick.com"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-3
meta-checked: "2026-11-09"
github-repo: "jordanbtucker/dpapick"
github-stars: 77
github-pushed: "2022-12-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dpapick

> Offline DPAPI forensic toolkit: decrypts Windows DPAPI blobs and master keys from Linux with a password, hash, or CREDHIST file.

## What it's for

DPAPIck (written by Jean-Michel Picod at Cassidian's cyber security center, with Ivan Fontarensky and Elie Bursztein) implements Microsoft's Data Protection API (DPAPI) offline: it decrypts the master keys and DPAPI blobs taken from a Windows profile given the user's password, password hash, or CREDHIST file — no running Windows system required. Application probes ship with the toolkit to decode protected secrets beyond raw blobs, including Google Chrome passwords, Dropbox, Google Talk, iCloud keychains, and LSA secrets from registry hives. It is Python-2-era software built on M2Crypto, with a `dpapidec` command and a library API; expect dependency work on modern systems.

## Typical use cases

### Decrypt DPAPI blobs offline with a password

```
dpapidec --masterkeydir ./Protect --credhist ./CREDHIST --password 'Password1' blob.bin
```

Outcome: loads every master key file from the directory, decrypts the matching ones with the password (falling back to the CREDHIST entry hash), then decrypts each blob given as an argument and prints the blob's GUIDs along with a representation of the recovered cleartext.

### Decrypt LSA secrets from registry hives (bundled probe)

```
./examples/lsasecrets --system SYSTEM.hive --security SECURITY.hive
```

Outcome: uses the toolkit's registry layer to unlock the SECURITY hive and prints each LSA secret's current and previous values, including their hex encodings with `--hex`.

*No corpus evidence — derived from upstream documentation.*
