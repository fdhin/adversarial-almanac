---
name: "pwdump"
categories:
  - blackarch-windows
version: "7.1"
homepage: "http://www.tarasco.org/security/pwdump_7/index.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom
meta-checked: "2026-11-09"
github-repo: "Seabreg/pwdump"
github-stars: 20
github-pushed: "2019-03-24"
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

# pwdump

> The classic Windows local hash dumper: extracts SAM account hashes from the live system or from offline hive files.

## What it's for

pwdump7 (tarasco.org) is the classic standalone credential dumper for Windows: run with administrator rights it pulls the local SAM database and the boot key it needs from the running system and prints every local account's LM and NTLM hashes, ready for offline cracking. It must be executed as an administrator because it accesses the raw disk device to read the hives rather than going through the registry API — which also lets most endpoint sensors notice it. For offline work, the -s flag takes SAM and SYSTEM hive files recovered from an image or another machine, so no live target is touched. The distribution pairs PwDump7.exe with an OpenSSL library (libeay32.dll) it needs at runtime.

## Typical use cases

### Dump local account hashes from a live host

Run it elevated on the target.

```
PwDump7.exe
```

Outcome: reads the local SAM and SYSTEM hives via the disk device, decrypts the password hashes with the boot key and prints each local account with its LM/NTLM hash pairs on stdout — feed the NTLM side into a cracking or pass-the-hash workflow.

### Extract hashes from captured hive files offline

Point it at hives collected from another system or a forensic image.

```
PwDump7.exe -s SAM SYSTEM
```

Outcome: uses the supplied SAM and SYSTEM hive files instead of the live disk and prints the account hashes without interacting with the running OS at all.

*No corpus evidence — derived from upstream documentation.*
