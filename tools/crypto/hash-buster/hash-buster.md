---
name: "hash-buster"
categories:
  - blackarch-crypto
version: "50.befb02a"
homepage: "https://github.com/UltimateHackers/Hash-Buster/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "UltimateHackers/Hash-Buster"
github-stars: 1906
github-pushed: "2024-12-10"
github-archived: false
github-issues: 26
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hash-buster

> Looks up hashes against online cracking APIs in one shot — no local wordlist or GPU needed.

## What it's for

hash-buster (invoked as `hash-buster` on BlackArch; the upstream script is named `buster`) is a Python 3 script that auto-identifies a hash's type and queries multiple online hash-lookup services for the plaintext, covering MD5, SHA1, SHA256, SHA384 and SHA512. It can also harvest hash strings out of files or whole directory trees and crack everything it finds, in parallel. It only recovers hashes already known to those public databases — it does not brute-force or mask-attack anything itself, and it sends your hashes to third-party APIs, which matters if the data is sensitive.

## Typical use cases

### Look up a single hash

Crack one digest without specifying its type.

```
hash-buster -s 21232f297a57a5a743894a0e4a801fc3
```

Outcome: the tool identifies the algorithm, queries its lookup sites and prints the recovered plaintext within a few seconds — or reports the hash as not found in any database.

### Harvest and crack hashes from a directory

Point it at a tree and let it find digests embedded in any file.

```
hash-buster -d /home/user/loot
```

Outcome: files under the directory are walked recursively, hash-like strings are extracted (even ones embedded in surrounding text), each is looked up, and matches are printed with their plaintext.

### Crack every hash in a list file

Process a prepared file where hashes appear alongside other content.

```
hash-buster -f hashes.txt
```

Outcome: every hash found in the file — including ones inside lines like `user@example.com:21232f297a57a5a743894a0e4a801fc3` or JSON blobs — is identified and looked up, with plaintexts reported per hash.

### Speed up bulk lookups with threads

Run lookups in parallel for large inputs.

```
hash-buster -f hashes.txt -t 10
```

Outcome: up to 10 concurrent requests to the lookup APIs, cutting total wall time on big hash lists.

*No corpus evidence — derived from upstream documentation.*
