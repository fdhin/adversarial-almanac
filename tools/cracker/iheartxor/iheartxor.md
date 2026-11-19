---
name: "iheartxor"
categories:
  - blackarch-cracker
version: "0.01"
homepage: "http://hooked-on-mnemonics.blogspot.com.es/p/iheartxor.html"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-16"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/iheartxor"
github-stars: 0
github-pushed: "2019-05-05"
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

# iheartxor

> Single-byte XOR key recovery for encoded strings inside binary data

## What it's for

iheartxor recovers text hidden by a single-byte XOR key in otherwise binary files — memory dumps, malware, firmware blobs. By default it scans for NUL-delimited non-NUL runs using its built-in pattern (regex `\x00(?!\x00).+?\x00`), tries every key from 0x1 through 0xff against each run, and prints the candidates that decode to fully printable text along with the offset and the key that produced them. It can also transform an entire file with one known key, which completes the workflow once a likely key is identified. Two limitations to know: the documented `-r` option for custom regular expressions is broken in version 0.01 (it dies with a NameError), so only the default NUL-delimited scan works, and the script is Python 2 based.

## Typical use cases

### Brute-force XOR keys over NUL-delimited strings

```
iheartxor binaryblob.bin
```

Outcome: for every NUL-delimited run the default pattern matches (very short runs are skipped), tries keys 0x1-0xff and prints one line per printable decoding in the form `<hex-offset> key <hex-key> <decoded text>`, so readable strings and their keys fall out of a binary blob in one pass.

### Decode a whole file with a known key

```
iheartxor -k 2f -f encoded.bin > decoded.bin
```

Outcome: XORs every byte of encoded.bin with the single-byte key 0x2f (keys are given in hex) and writes the raw decoded stream to stdout — a pure transform with no scanning or filtering.

*No corpus evidence — derived from upstream documentation.*
