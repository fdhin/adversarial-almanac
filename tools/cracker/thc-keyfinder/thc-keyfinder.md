---
name: "thc-keyfinder"
categories:
  - blackarch-cracker
version: "1.0"
homepage: "https://www.thc.org/releases.php"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-15"
build-system: other
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# thc-keyfinder

> Locates cryptographic key material, encrypted and compressed data inside binary files via entropy analysis.

## What it's for

When you hold a memory dump, firmware image or arbitrary blob that probably contains a private key, this tool slides a key-sized window across the file and scores each window with three randomness checks — entropy, arithmetic mean and a counting-byte detector — an improvement on the Shamir/van Someren "Playing hide and seek with stored keys" technique. It is a locator, not a parser or cracker: it prints a hexdump of regions that statistically look like key material, and the hit is an indicator, not a byte-exact match — expect false positives and boundaries a few bytes off. It cannot decrypt anything or tell you the key's format.

## Typical use cases

### Default scan for a 1024-bit RSA key

Score a binary with the defaults: 128-byte window, 85% randomness threshold.

```
keyfinder memory.bin
```

Outcome: prints a hexdump of every region above the randomness threshold with its file offset — candidate key material to extract and inspect with a parser.

### Hunt a 4096-bit RSA key

Raise the key size and, per the tool's own tuning hints, the randomness threshold with it.

```
keyfinder -k 512 -r 95 memory.bin
```

Outcome: same candidate-region hexdumps, but with 512-byte (4096-bit) windows and only ~95%-random regions reported — fewer, larger candidates.

### Find smaller key material

Lower key size, window and threshold together (hints: `-w` should be 1/8–1/20 of `-k`; smaller `-k` wants a smaller `-r`).

```
keyfinder -k 64 -w 8 -r 75 firmware.bin
```

Outcome: candidate regions for 64-byte (512-bit) material such as symmetric keys or certificate blobs; more false positives than the defaults, which is the trade-off for smaller keys.

### Debug why regions are rejected

See the scoring values behind accept/reject decisions while tuning thresholds.

```
keyfinder -d image.bin
```

Outcome: prints per-window debug output — entropy in bits (8 is fully random), mean value (127.5 is fully random) and `Counting detected, false positive, ignoring...` messages — so you can pick better `-r`/`-w` values.

*No corpus evidence — derived from upstream documentation.*
