---
name: "rsactftool"
categories:
  - blackarch-crypto
version: "695.4ffadb0"
homepage: "https://github.com/RsaCtfTool/RsaCtfTool"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "RsaCtfTool/RsaCtfTool"
github-stars: 7216
github-pushed: "2026-10-23"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 163
gh-activity-checked: "2026-11-09"
---

# rsactftool

> Recovers RSA private keys and plaintexts from weak parameters by chaining dozens of factorization and lattice attacks in one run.

## What it's for

RsaCtfTool is a multi-attack RSA recovery tool: give it a public key file — or raw parameters such as `n` and `e`, a leaked prime, or partial private-key material — and it sweeps a long list of attacks to factor the modulus, recover the private key, and decrypt ciphertexts. The arsenal spans Wiener, Boneh-Durfee, Fermat, Pollard rho and p-1, ECM, quadratic sieve, factordb lookups, ROCA detection, Coppersmith/lattice attacks on partial knowledge of p or d, dp/dq leaks, Bellcore fault signatures, and a set of gimmick-prime checks. It supports textbook semiprime moduli only (not multiprime), works best on small or weakly generated keys, and explicitly defers serious factoring jobs to dedicated tools. It also ships key utilities: build a public key from raw parameters, dump key components, convert SSH public keys to PEM.

## Typical use cases

### Recover the private key from a weak public key

```
rsactftool --publickey key.pub --private
```

Outcome: runs the default attack chain against the key; on success prints the recovered private key in PEM on stdout — redirect it or pass `--output key.pem` to save it.

### Decrypt a ciphertext with a weak key

```
rsactftool --publickey key.pub --decryptfile ciphertext.bin
```

Outcome: recovers the private key, then decrypts `ciphertext.bin` and prints the plaintext; multiple files can be given comma-separated.

### Rebuild the private key from a known prime

When you already hold the modulus, one prime factor and the exponent, skip the attacks entirely.

```
rsactftool -n <modulus> -q <prime> -e 65537 --private
```

Outcome: derives the second prime from n/q, computes d and prints the PEM private key directly — all numeric arguments accept decimal or `0x`-prefixed hex.

### Attack a bare modulus taken from a token or script

```
rsactftool -n <modulus> -e 65537 --private
```

Outcome: when parameters were lifted from somewhere other than a key file (a JWT header, source code, configuration), passing them as `-n`/`-e` lets the attack chain run directly against the raw integers.

### Run one specific attack

```
rsactftool --publickey key.pub --attack wiener --private
```

Outcome: tries only the named attack (`wiener`, `boneh`, `fermat`, `factordb`, `ecm`, `partial_p`, ...) instead of the full chain — faster when the key's weakness is already suspected.

### Create a public key from raw parameters

```
rsactftool --createpub -n <modulus> -e 65537
```

Outcome: prints a PEM public key built from the given modulus and exponent, then exits — useful to turn scattered parameters into a key file other tooling accepts.

### Inspect a key's parameters

```
rsactftool --dumpkey --key key.pub
```

Outcome: prints n, e, d, p and q of the key without running any attack; add `--ext` to include the CRT parameters dp, dq, pinv and qinv.
