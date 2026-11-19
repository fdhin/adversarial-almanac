---
name: "ares"
categories:
  - blackarch-cracker
version: "v0.10.0.r110.ga63614a"
homepage: "https://github.com/bee-san/Ares"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-03-01"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "bee-san/Ares"
github-stars: 879
github-pushed: "2026-11-02"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 309
gh-activity-checked: "2026-11-09"
---

# ares

> Automatically decodes encoded or enciphered text when you don't know the encoding or cipher used.

## What it's for

Ares (bee-san's Rust successor to ciphey) takes a blob of ciphertext — base encodings, Caesar/ROT/Vigenère-class ciphers, reversed text, layered combinations — and searches chains of decoders until it produces something that reads as natural language, then prints the plaintext and the decoding path. It prioritises decoders statistically as it runs (popular chains like base64 → base64 get explored first) and can ask you to confirm that a candidate is readable. Give it text on the command line or in a file; a wordlist and top-results mode help when you know fragments of the expected plaintext. It recovers encoded and classical-ciphered text — it does not break modern cryptography with strong keys.

## Typical use cases

### Decode text passed on the command line

```
ares -t "dGhpcyBpcyBhIHRlc3Q="
```

Outcome: searches decoder chains and prints the recovered plaintext together with the sequence of transformations that produced it.

### Decode a file without the interactive prompt

```
ares -f ciphertext.txt --disable-human-checker
```

Outcome: reads the input from a file and skips the "is this readable?" confirmation so it can run unattended in scripts and pipelines.

### Guide the search with a wordlist

```
ares -f ciphertext.txt --wordlist names.txt --top-results
```

Outcome: matches decoded candidates against the newline-separated words in the wordlist and, in top-results mode, keeps searching past the first hit and prints all ranked candidates when the timer expires.

*No corpus evidence — derived from upstream documentation.*
