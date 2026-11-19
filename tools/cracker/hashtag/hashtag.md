---
name: "hashtag"
categories:
  - blackarch-cracker
version: "0.41"
homepage: "https://github.com/SmeegeSec/HashTag"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "SmeegeSec/HashTag"
github-stars: 222
github-pushed: "2026-01-23"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# hashtag

> Identifies password hash types from a single hash, a file of hashes, or a whole directory tree, and maps each one to candidate algorithms and Hashcat cracking modes.

## What it's for

Given a hash of unknown format, telling MD5 from MD5(UNIX) from Cisco-IOS MD5 from Half-MD5 by eye is guesswork; HashTag (packaged as `hashtag`) does it mechanically. It takes one of three inputs — a single hash, a file of hashes, or a directory it walks recursively — and reports every hash algorithm each input could be, including many formats that Hashcat supports. It does not crack anything itself: the output is identification plus Hashcat mode numbers, which you feed to the actual cracking tool. Unidentifiable hashes can be kept in the output with `-n` so nothing is silently dropped.

## Typical use cases

### Identify a single hash

```
hashtag -sh 2ac9cb7dc02b3c0083eb70898e549b63
```

Outcome: prints the input hash together with every hash type it matches (e.g. raw MD5 and its look-alikes), so you know which cracking mode applies before spending time on it. This is the quick pre-check to run before handing a hash to a cracking tool.

### Parse a file of harvested hashes

```
hashtag -f hashes.txt -o identified.txt
```

Outcome: reads `hashes.txt` line by line, identifies each hash, and writes the full list of identified hashes to `identified.txt` (default output path is `./HashTag/HashTag_Output_File.txt` when `-o` is omitted). Add `-n` to also include hashes it could not identify in the output file.

### Sweep a directory tree and split results by Hashcat mode

```
hashtag -d ./loot -hc
```

Outcome: recursively traverses `./loot`, locates files containing hashes, and identifies what it finds. With `-hc`, it additionally writes a separate output file per hash type keyed by Hashcat mode, so each file can be passed straight to `hashcat -m <mode>` without further filtering.

*No corpus evidence — derived from upstream documentation.*
