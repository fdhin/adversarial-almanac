---
name: "pemcracker"
categories:
  - blackarch-cracker
version: "9.a741c93"
homepage: "https://github.com/bwall/pemcracker.git"
packaged-by: [blackarch]
blackarch-builddate: "2015-11-20"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "bwall/pemcracker"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# pemcracker

> Multi-threaded passphrase recovery for encrypted PEM private keys (Brian Wallace, @botnet_hunter) — the same job as pemcrack but across every CPU core, with the key loaded into memory once per thread.

*No corpus evidence — derived from upstream documentation. The original GitHub repo is gone (frontmatter `github-status: gone`); the exact packaged commit `a741c93` is preserved by mirrors (e.g. H4CK3RT3CH/pemcracker), whose README and sources this note was verified against — the output shape was confirmed by building and running that source.*

## What it's for

The same problem as pemcrack — an encrypted PEM private key whose passphrase you need back — attacked in parallel: instead of re-reading the key from disk for every guess, each worker thread keeps the key material in memory and runs its share of the wordlist, so a run scales across all cores. Keys are read through OpenSSL's `PEM_read_PrivateKey`, covering the classic encrypted PEM formats of its era. The interface is deliberately minimal — a PEM file plus a word file, no flags — and the project is an abandoned 2013-era side project by its own description; treat it as a quick parallel wordlist runner rather than a maintained cracker.

## Typical use cases

### Run a wordlist against an encrypted key on all cores

```
pemcracker key.pem wordlist.txt
```

Outcome: spins up workers across the CPU and grinds through the wordlist; on a hit prints `Password is <passphrase> for key.pem` and exits — verified against the project's own test pair (test.pem/test.dict, passphrase `komodia`). If no wordlist entry works it ends without a match.

### Check the interface

```
pemcracker
```

Outcome: prints `pemcracker 0.1.0` and the one-line usage `pemcracker <path to pem> <word file>` then exits — that is the entire option surface; thread count is self-configured, there are no other flags.
