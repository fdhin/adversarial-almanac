---
name: "cisco7crack"
categories:
  - blackarch-cracker
version: "2.f1c21dd"
homepage: "https://github.com/madrisan/cisco7crack"
packaged-by: [blackarch, aur]
blackarch-builddate: "2015-03-21"
build-system: other
license: GPL2
meta-checked: "2026-11-09"
github-repo: "madrisan/cisco7crack"
github-stars: 15
github-pushed: "2022-01-24"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cisco7crack

> Instantly decodes and produces Cisco type-7 password strings — the XOR encoding IOS uses for keys and legacy passwords.

## What it's for

cisco7crack converts Cisco type-7 strings — the reversible XOR-encoded values IOS shows for `username ... password 7`, WLAN pre-shared keys, and similar config lines — into plaintext, and back. Decoding is a fixed-key XOR, so it is instantaneous and always succeeds on well-formed input; encoding a plaintext yields one of 16 possible ciphertexts depending on the key offset, and the tool can pick a random offset, a chosen one, or show all 16. Type-7 is an encoding, not a hash: no search is involved, unlike type-5 `enable secret` hashes (see cisco7crack's sibling cisco5crack).

## Typical use cases

### Decode a type-7 string
```
cisco7crack 082F1C5A1A490D43000F5E033F78373B
```
Outcome: prints the ciphertext and its plaintext in two labelled lines (`Encrypted string : ...`, `Plain string     : ...`) — decoding is the default action, no flag required; the sample above is the tool's own documented example and decodes to `n0ts0h4rd2gu3ss`.

### Encode a plaintext as type-7
```
cisco7crack -c 'Sup3rS3cret'
```
Outcome: prints a valid type-7 encoding of the plaintext with a randomly chosen key offset — a paste-ready `password 7` value for config generation or lab setup.

### Produce a chosen or all encodings
```
cisco7crack -c#3 '@l1c3&b0b'
```
Outcome: prints the encoding that uses key offset 3 of the possible 0-15; replace `#3` with `a` (`-ca`) to print all 16 encodings of the same plaintext in a column.

### Use in shell scripts
```
cipher=$(cisco7crack -cq 'b@shscr1pt')
```
Outcome: `-q` suppresses the labelled header lines so stdout is the bare ciphertext (e.g. `06042F32445D0A0B540706`), making command substitution capture exactly one value.

*No corpus evidence — derived from upstream documentation.*
