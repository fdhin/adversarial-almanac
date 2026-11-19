---
name: "auto-xor-decryptor"
categories:
  - blackarch-crypto
version: "7.2eb176d"
homepage: "https://github.com/MRGEffitas/scripts"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "MRGEffitas/scripts"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# auto-xor-decryptor

> Automatically recovers short XOR keys from an XOR-encrypted binary by validating candidate decryptions against patterns in the output, then writes the decrypted file.

## What it's for

Single-byte or short repeating-key XOR is one of the most common ad-hoc obfuscations in malware and CTF-style artifacts; this Python 3 tool (MRG Effitas' `auto_xor_decryptor.py`, installed as `auto-xor-decryptor`) removes the manual frequency-analysis step. Give it an encrypted binary and it searches for a short XOR key, tries candidate keys, and keeps the one whose decryption shows a recognizable pattern in the first bytes of the result. It is built for short keys — if the key is long or the plaintext has no detectable structure in the search window, the automatic mode will not find it, and you can either supply the key yourself or loosen the tuning parameters. It does nothing beyond XOR: no other cipher, no compression handling.

## Typical use cases

### Auto-crack an XOR-encrypted file with defaults

```
auto-xor-decryptor --input encrypted.bin --output decrypted.bin
```

Outcome: brute-forces candidate short XOR keys, scores each trial decryption by looking for meaningful patterns in the decrypted output (search capped by `--patternmaxsearch`, default 500), and writes the best decryption to `decrypted.bin` (both file names default to `encrypted` and `decrypted` when the flags are omitted). Check the output; a wrong key yields garbage with no readable structure.

### Decrypt with a known key

```
auto-xor-decryptor --input encrypted.bin --output decrypted.bin --xorkeyhex 31323334
```

Outcome: skips the search and XORs the whole input with the given key — here the hex bytes `31 32 33 34` — writing the result to `decrypted.bin`. Use `--xorkeyascii ijkl` to specify the key as ASCII instead of hex. If the ciphertext starts mid-key (for example it was cut out of a larger encrypted stream), add `--offset N` to rotate the key alignment before applying it.

### Widen the automatic search

```
auto-xor-decryptor --input encrypted.bin --output decrypted.bin --keyminlen 4 --patternmaxsearch 1000
```

Outcome: raises the minimum key length the search considers (default 2, measured in hex characters) and doubles the window of decrypted output scanned for validating patterns (default 500). Reach for this when the default run fails on a file you believe is short-key XOR — longer candidate keys and a larger validation window catch keys the defaults miss, at the cost of a slower search.

*No corpus evidence — derived from upstream documentation.*
