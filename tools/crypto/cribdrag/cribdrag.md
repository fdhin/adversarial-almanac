---
name: "cribdrag"
categories:
  - blackarch-crypto
version: "5.2d27dbf"
homepage: "https://github.com/SpiderLabs/cribdrag"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "SpiderLabs/cribdrag"
github-stars: 177
github-pushed: "2020-08-07"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cribdrag

> Interactive crib-dragging tool for ciphertext produced by XOR with a reused or predictable keystream.

## What it's for

cribdrag performs crib dragging attacks against ciphertext encrypted with an XOR operation and a predictable key: one-time pads with a reused key, stream ciphers with a reused key, and single- or multiple-character XOR. The companion `xorstrings.py` XORs two same-length ciphertexts encrypted under the same keystream, eliminating the key entirely; `cribdrag.py` then lets you interactively drag guessed plaintext fragments ("cribs") along that result, printing every offset where the crib produces printable output so keystream and both plaintexts can be recovered byte by byte. It is a Python 2 script by Daniel Crowley (Trustwave/SpiderLabs), released after the DEF CON 21 bitcoin party, and is no longer under active maintenance.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### XOR two ciphertexts that share a keystream

```
python xorstrings.py 1c0b1d0a0e1f04111c0b1d0a0e1f0411 3d2a1c0f0b1e05123d2a1c0f0b1e0512
```

Outcome: prints the hex-encoded XOR of the two ciphertexts; because both were encrypted with the same keystream, the result is plaintext XORed with plaintext — feed this string to `cribdrag.py` as the ciphertext argument.

### Drag cribs along the XORed ciphertext

```
python cribdrag.py 21161605050101132116160505010113
```

Outcome: starts an interactive session that prompts for a crib; each guessed fragment typed at the prompt is tested at every offset and the positions where the XOR yields printable characters are shown, so repeated guesses progressively recover both messages and the reused keystream.

### Restrict the candidate charset

```
python cribdrag.py -c " abcdefghijklmnopqrstuvwxyz" 21161605050101132116160505010113
```

Outcome: crib results are filtered against the given charset, suppressing garbage offsets when the underlying plaintext is known to be lowercase letters and spaces; the default charset covers printable ASCII, so narrowing it speeds up manual triage of long ciphertexts.
