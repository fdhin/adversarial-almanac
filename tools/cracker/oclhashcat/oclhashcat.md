---
name: "oclhashcat"
categories:
  - blackarch-cracker
version: "2.01"
homepage: "http://hashcat.net/oclhashcat/"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-24"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# oclhashcat

> The legacy OpenCL/CUDA edition of hashcat (pre-v3), superseded as a standalone tool by modern hashcat.

## What it's for

oclHashcat was the GPU branch of the hashcat family through v2.01 (December 2015): a binary-only distribution that unified the earlier oclHashcat-plus and oclHashcat-lite tools, offering 150+ hash types, GPU-side rule application, straight/combination/brute-force/hybrid attack modes, sessions and restore. Since hashcat 3.0 (2016) the CPU and GPU crackers are merged into the single open-source `hashcat` binary and oclHashcat is no longer developed — use current hashcat for any real workload; this note documents the historical package distributions still ship. On BlackArch the `oclhashcat` wrapper executes `oclHashcat64.bin` from the package directory (32-bit hosts run `oclHashcat32.bin`).

## Typical use cases

### Straight dictionary attack against salted md5crypt hashes

```
oclhashcat -m 500 md5crypt.txt wordlist.dict
```

Outcome: GPU cracking of the `$1$`-style unix md5crypt hashes in `md5crypt.txt` starts with the device/status display; recovered plaintext:hash pairs are appended to the pot file and listed as found. Mode 400 attacks `$H$` phpBB/phpass hashes identically.

### Hybrid mask + dictionary attack

Combine every dictionary word with generated suffixes.

```
oclhashcat -t 32 -a 7 hashes.txt ?a?a?a?a wordlist.dict
```

Outcome: attack mode 7 appends all four-character mask combinations (`?a?a?a?a`) to each word of `wordlist.dict`, keeping up to 32 (`-t`) markov candidates per hash in the kernel — this is the shipped example-0 workload.

### Feed candidates from stdin

Pipe an external candidate generator.

```
cat wordlist.dict | oclhashcat -m 400 phpass.txt
```

Outcome: words are read from standard input instead of a file argument, so any producer — a generator, combinator or decompressor — can stream candidates straight into the GPU loop.

*No corpus evidence — derived from upstream documentation.*
