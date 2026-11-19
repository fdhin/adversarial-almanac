---
name: "cisco5crack"
categories:
  - blackarch-cracker
version: "2.c4b228c"
homepage: "https://github.com/madrisan/cisco5crack"
packaged-by: [blackarch]
blackarch-builddate: "2024-02-04"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "madrisan/cisco5crack"
github-stars: 4
github-pushed: "2018-05-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cisco5crack

> Generates and cracks Cisco IOS type-5 (`$1$` MD5) enable secrets and PIX firewall password hashes.

## What it's for

cisco5crack works with Cisco's type-5 password hashing. It generates IOS `enable secret`-style MD5 crypt hashes (`$1$...`) from a plaintext — with a chosen, random, or device-sourced salt — generates PIX firewall hashes, and attacks existing hashes either by dictionary or by regex-driven brute force with length bounds. Cracking sessions can run daemonized, persist their position, and resume after interruption, which matters because MD5 crypt is deliberately slow per guess. It handles only type-5/PIX MD5 hashes; the reversible type-7 encoding is the domain of its sibling cisco7crack.

## Typical use cases

### Generate an IOS type-5 hash
```
cisco5crack --crypt-ios --salt=xyzt 'c1sc0s3cr3t'
```
Outcome: prints the `$1$xyzt$...` MD5 hash in exactly the form IOS stores for `enable secret 5`; the salt must be 4 characters, and omitting `--salt` forges a random one.

### Generate a PIX firewall hash
```
cisco5crack --crypt-pix weakpw
```
Outcome: prints the PIX-formatted hash of the plaintext (PIX hashes are unsalted), usable in `passwd` lines of a PIX config.

### Dictionary-attack a captured hash
```
cisco5crack --dictionary=./words.lst -l10 '$1$salt$q1U/vVn2cZb7JG3sxRad60'
```
Outcome: tries every candidate of the given length range from `words.lst` against the hash and prints the recovered plaintext on a hit; drop `-l` to try all lengths.

### Brute-force with a character-set expression
```
cisco5crack -b '[:lower:]{5}' '$1$dDoS$FxuqrTObFaJCC8sIOq2XM.'
```
Outcome: enumerates only 5-letter lowercase candidates — the regex accepts classes (`[:alnum:]`), ranges (`[a-z0-9]`), and repetition (`{n}`) — so a narrow expression is what keeps the keyspace tractable; prints the plaintext on a match.

### Resume an interrupted cracking session
```
cisco5crack -r
```
Outcome: continues a previously interrupted attack from the default restore file (`-r <file>` for an explicit one); with `-D` the session runs daemonized and its position is saved automatically.

*No corpus evidence — derived from upstream documentation.*
