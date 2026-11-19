---
name: "featherduster"
categories:
  - blackarch-crypto
version: "191.9229158"
homepage: "https://github.com/nccgroup/featherduster"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nccgroup/featherduster"
github-stars: 1141
github-pushed: "2021-12-02"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# featherduster

> Interactive cryptanalysis console that profiles ciphertexts and runs the matching attacks.

## What it's for

FeatherDuster (NCC Group, principally Daniel "unicornfurnace" Crowley) makes breaking weak crypto as easy as possible: import ciphertext samples and it auto-detects common encodings (Base64, ASCII-hex, zlib, URL), then profiles the samples — block vs stream cipher, ECB, CBC with fixed IV, low entropy, hash algorithms, OpenSSL salted formatting, keystream reuse, undersized or reused RSA moduli, transposition-only ciphers — and suggests the attack modules that fit those properties, which you run from the same console. The engine behind it, Cryptanalib, is a standalone Python library for building custom attacks. Use it when you hold unknown ciphertext and do not know what produced it; it attacks flawed or misused crypto, not correctly deployed algorithms. The codebase is Python 2.

## Typical use cases

### Launch the console with sample files

```
featherduster ciphertext1.bin ciphertext2.bin
```

Outcome: each positional file becomes one distinct sample (encoded files are auto-decoded) and you land at the `featherduster>` prompt.

### Enter samples without files

```
featherduster> import manualentry
```

Outcome: interactively prompts for one sample per line; `import singlefile` (whole file as one sample), `import multifile` (newline-separated sample list) and `import clear` are the other import modes.

### Profile the samples

```
featherduster> analyze
```

Outcome: prints detected properties — e.g. that samples do not align to any block size and look like a stream cipher or XOR rather than a block cipher — followed by `[+] Suggested modules:` naming the applicable attacks; if an encoding was detected it first offers to decode the samples.

### Find the right attack module

```
featherduster> modules
featherduster> search xor
```

Outcome: `modules` lists every loaded attack with its description; `search <keyword>` filters by name and description so you can locate e.g. the single- and multi-byte XOR solvers.

### Run every applicable attack

```
featherduster> autopwn
```

Outcome: re-analyzes the samples and automatically executes each module whose keywords match the detected properties, printing recovered plaintexts and keys as they fall out; `use <module>` selects a single module interactively instead.

### Use Cryptanalib directly

```
python2 -c "import cryptanalib as ca; print '\n'.join(f for f in dir(ca) if f.startswith('break_'))"
```

Outcome: lists the library's attack functions (`break_alpha_shift`, `break_many_time_pad`, `bb98_padding_oracle`, ...); `help(ca.<function>)` prints its signature and docs for building custom tools.
