---
name: "nomorexor"
categories:
  - blackarch-crypto
version: "2.84489f9"
homepage: "https://github.com/hiddenillusion/NoMoreXOR"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hiddenillusion/NoMoreXOR"
github-stars: 88
github-pushed: "2018-06-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nomorexor

> Guesses a file's single-byte XOR key by frequency analysis, then verifies the guess with YARA rules.

## What it's for

NoMoreXOR (packaged as the `nomorexor` command, a link to `NoMoreXOR.py`) helps
recover plaintext from files obfuscated with a single-byte XOR key — a common
malware obfuscation. It converts the file to a hex dump, reports the most
common bytes (XOR preserves byte frequency, so frequent ciphertext bytes point
at the key when XORed against expected plaintext bytes such as `0x00`), and can
try all 256 possible keys automatically, writing each de-XORed candidate and
scanning it with YARA rules to flag the one that decodes to something
recognizable. It is a triage helper, not a general cryptanalysis tool: the
guessing targets single-byte keys, and only the manual `-xor` mode accepts a
longer (repeating) hex key.

## Typical use cases

### Convert a sample to the hex file and inspect byte frequencies

Produce the intermediate hex file and look at the frequency hints before
committing to a key.

```
nomorexor -c -o sample.hex sample.bin
nomorexor -g sample.hex
```

Outcome: the first command writes `sample.hex` (a hex-encoded copy of the
input); the second prints the most common characters and candidate XOR keys
derived from frequency analysis, giving you likely keys to try.

### Auto-analyze with YARA to find the correct key

Try all 256 keys and let YARA decide which decoding is real.

```
nomorexor -a -o analyzed.bin -y rules.yar sample.bin
```

Outcome: de-XORs the file with each candidate key and scans every result with
`rules.yar` (or the hardcoded default when `-y` is omitted); hits identify the
correct key and produce the decoded output file. Requires `-o` since every
candidate needs to be written to disk.

### De-XOR with a key you already know

Apply a known key directly, skipping the guessing.

```
nomorexor -xor 35 -o decoded.bin sample.bin
```

Outcome: writes `decoded.bin`, the input XORed with the repeating key given as
hex byte pairs (`35` = the single byte 0x35; longer keys like `deadbeef` cycle
over the file).

*No corpus evidence — derived from upstream documentation.*
