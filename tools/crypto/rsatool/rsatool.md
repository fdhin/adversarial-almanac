---
name: "rsatool"
categories:
  - blackarch-crypto
version: "34.b34862a"
homepage: "https://github.com/ius/rsatool"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "ius/rsatool"
github-stars: 1682
github-pushed: "2026-07-20"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# rsatool

> Calculates RSA and RSA-CRT parameters from primes or partial key material and writes an OpenSSL-compatible private key.

## What it's for

rsatool is the construction counterpart to key-cracking tools: given either the two primes (p, q) or the modulus and private exponent (n, d), it computes the complete RSA parameter set — n, e, d, p, q and the CRT values dP, dQ, qInv — and writes an OpenSSL-compatible DER or PEM private key. Reach for it when the key material is already known (recovered prime factors, parameters scattered across a challenge or a leak) and you need a usable key file for OpenSSL or other tooling. It builds keys; it does not attack, factor, or crack anything.

## Typical use cases

### Build a PEM key from modulus and private exponent

```
rsatool.py -f PEM -o key.pem -n 13826123222358393307 -d 9793706120266356337
```

Outcome: displays the full parameter set (p, q, n, e, d, dP, dQ, qInv) and writes the RSA private key to `key.pem` in PEM format, ready for `openssl rsa -in key.pem -check`.

### Build a DER key from two primes

```
rsatool.py -f DER -o key.der -p 4184799299 -q 3303891593
```

Outcome: derives every remaining parameter from the two primes — the public exponent defaults to 65537 unless overridden with `-e` — and writes the DER-encoded private key to `key.der`.

*No corpus evidence — derived from upstream documentation.*
