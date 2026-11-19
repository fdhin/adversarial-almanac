---
name: "findmyhash"
categories:
  - blackarch-crypto
version: "1.1.2"
homepage: "https://code.google.com/archive/p/findmyhash/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "frdmn/findmyhash"
github-stars: 88
github-pushed: "2021-04-15"
github-archived: false
github-issues: 15
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# findmyhash

> Cracks hashes by handing them to free online lookup services.

## What it's for

findmyhash submits hashes to a curated set of free online cracking services and reports the plaintext when one of them knows it. Supported types cover MD4, MD5, SHA-1/224/256/384/512, RMD160, GOST, WHIRLPOOL, LM, NTLM, MySQL, Cisco IOS type 7, Juniper `$9$` and the base64 LDAP MD5/SHA1 variants. Use it as the cheap first pass before local brute-forcing — popular hashes fall in seconds with zero compute. The flip sides: it is online-only (unknown hashes remain unknown), everything submitted goes to third parties, and it is an unmaintained Python 2 script — the upstream homepage is the archived Google Code project; the GitHub repo is a mirror (packaged on BlackArch as `/usr/bin/findmyhash`).

## Typical use cases

### Crack a single hash

```
findmyhash MD5 -h "098f6bcd4621d373cade4e832627b4f6"
```

Outcome: walks the online services and prints the recovered plaintext (here `test`) if any service knows the hash, otherwise reports it as not found.

### Crack a file of same-type hashes

```
findmyhash MYSQL -f mysqlhashes.txt
```

Outcome: reads one hash per line and cracks each in turn through the services; every hash in the file must be of the same algorithm.

### Fall back to a web search

```
findmyhash SHA1 -h "a94a8fe5ccb19ba61c4c0873d391e987982fbbd3" -g
```

Outcome: when no service cracks the hash, searches the hash string on Google and shows the results — only valid in single-hash `-h` mode.

### Submit an LM/NTLM pair

```
findmyhash NTLM -h "9a5760252b7455deaad3b435b51404ee:0d7f1f2bdeac6e574d6e18ca85fb58a7"
```

Outcome: passes both halves of the `LM:NTLM` pair together — the recommended input format — so the services can crack either side.

*No corpus evidence — derived from upstream documentation.*
