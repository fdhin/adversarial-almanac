---
name: "xorbruteforcer"
categories:
  - blackarch-crypto
version: "0.1"
homepage: "http://eternal-todo.com/category/bruteforce"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "Seabreg/xorBruteForcer"
github-stars: 0
github-pushed: "2019-05-24"
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

# xorbruteforcer

> Single-byte XOR brute-forcer that filters 256 candidate keys by a search pattern.

## What it's for

xorBruteForcer (Jose Miguel Esparza, eternal-todo.com) answers one question: is this file single-byte XOR encoded, and if so with which key? It tries all 256 key values — or one key you supply — and either prints every decoded variant or only the variants matching a search pattern. The pattern form is the practical one: give it a string that must appear in the decoded output (`GET /`, `MZ`, a domain) and it reports exactly which keys produce it. It is a Python 2 script (the packaged copy uses a python2 shebang), handles single-byte keys only — no multi-byte or repeating keys, and no ROL/ROT/SHIFT/ADD transforms, which are the domain of XORSearch.

## Typical use cases

### Brute-force the key filtered by a known plaintext

```
xorbruteforcer ciphertext.bin 'GET /'
```

Outcome: prints each matching decoding inside `[0X??]` ... `[/0X??]` markers followed by `Pattern found using the following keys: ['0X41']`; exits with `Warning: Pattern not found!!` when no single-byte key yields the pattern (case-insensitive match).

### Decode with a known key

```
xorbruteforcer -k 41 ciphertext.bin
```

Outcome: applies hex key 41 and prints the single decoding between `[0X41]` markers — useful to extract the payload once a key is known.

### Dump all 256 single-byte variants

```
xorbruteforcer ciphertext.bin | less
```

Outcome: prints every possible single-byte XOR decoding, each bracketed by its key marker, for manual inspection when no reliable search string exists.

*No corpus evidence — derived from upstream documentation.*
