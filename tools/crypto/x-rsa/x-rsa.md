---
name: "x-rsa"
categories:
  - blackarch-crypto
version: "166.291d4c7"
homepage: "https://github.com/X-Vector/X-RSA"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "X-Vector/X-RSA"
github-stars: 108
github-pushed: "2023-06-04"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# x-rsa

> Interactive menu of 15 classical RSA cryptanalysis attacks (Håstad, Wiener, Boneh-Durfee, common modulus, CRT, ...).

## What it's for

X-RSA is a Python 3 assistant for breaking weak RSA parameter sets. Instead of choosing a library and writing the math yourself, you launch it, pick the attack that matches the parameters you hold (ciphertext plus modulus, primes, private exponent, CRT components, or multiple ciphertexts), and paste each value at the prompts; it prints the recovered plaintext in decimal, hex and ASCII. It covers the standard attack families — Håstad broadcast, common modulus, common factor, Wiener and Boneh-Durfee small-exponent attacks, multi-prime and partial-key (dp/dq) cases — plus a number-factorization mode for directly factoring an unfactored modulus. It is entirely interactive: no files, no scripting interface, one attack per run, and you must recognize which attack fits your parameter set.

## Typical use cases

### Run Wiener's attack with (c, n, e)

```
x-rsa
# menu: 1 (Attacks) -> 9 (Wiener), then enter c, n, e at the prompts
```

Outcome: computes the private key from a small private exponent and prints the decrypted message as decimal, hex and decoded ASCII text. Run as `python3 Attack.py` from a source checkout; `x-rsa` is the packaged wrapper.

### Break an e=3 broadcast with three ciphertexts

```
x-rsa
# menu: 1 -> 6 (Hasted), then enter c1, c2, c3, n1, n2, n3 (e is 3)
```

Outcome: combines the three ciphertexts with the Chinese Remainder Theorem and takes the integer cube root, yielding the plaintext of a message sent under the same exponent to three moduli.

### Recover a message shared across two exponents of the same modulus

```
x-rsa
# menu: 1 -> 7 (Common Modulus), then enter c1, c2, e1, e2, n
```

Outcome: uses the extended Euclidean algorithm over the two exponents to reconstruct the plaintext without knowing either private key — applies when the same message was encrypted to two public keys sharing one modulus.

### Decrypt with leaked CRT components

```
x-rsa
# menu: 1 -> 8 (Chinese Remainder Theorem), then enter c, p, q, dp, dq
```

Outcome: reassembles the message from the CRT private-key components (dp, dq), the typical form of a partially leaked key.

### Factor a modulus directly

```
x-rsa
# menu: 2 (Factorization Number), then enter n
```

Outcome: attempts factorization of the modulus (quadratic-sieve based) and reports the primes, which then feed any of the (c, p, q, e) attack options.

*No corpus evidence — derived from upstream documentation.*
