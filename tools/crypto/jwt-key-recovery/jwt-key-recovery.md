---
name: "jwt-key-recovery"
categories:
  - blackarch-crypto
version: "11.094df95"
homepage: "https://github.com/FlorianPicca/JWT-Key-Recovery"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "FlorianPicca/JWT-Key-Recovery"
github-stars: 138
github-pushed: "2023-05-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jwt-key-recovery

> Recover the public key used to sign JWT tokens from the tokens themselves.

## What it's for

A small Python tool (upstream `recover.py`, packaged as the `jwt-key-recovery` command) that derives the public key behind an RSA- or ECDSA-signed JSON Web Token purely from the token data. RSA algorithms require two tokens signed with the same key; ECDSA works from a single token, though two tokens guarantee a unique result — from one token up to two candidate keys are possible and both are printed. Typical purposes: verifying a signature when the JWKS endpoint or public key is unavailable, checking whether two services share a signing key, and reconstructing keys for further analysis. It recovers public keys only — it does not forge signatures or recover private keys.

## Typical use cases

### Recover an RSA public key from two tokens

```
jwt-key-recovery eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9... eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
```

Outcome: auto-detects the algorithm from the token header, prints `Recovering public key for algorithm RS256...`, then the modulus `n`, exponent `e` and the key as a PEM `-----BEGIN PUBLIC KEY-----` block.

### Recover an ECDSA public key from a single token

```
jwt-key-recovery -v eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9...
```

Outcome: recovers the ECDSA point and prints the x/y coordinates plus the PEM public key; when the signature admits two possible keys it reports that both are displayed. `-v` adds verbose debugging output.

### Recover with a non-default RSA exponent

```
jwt-key-recovery -e 3 eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9... eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
```

Outcome: performs RSA recovery assuming public exponent 3 instead of the default 65537; similarly, `-f N` raises the bound of small factors removed during recovery from the default 2000, and `-compressed` selects the compressed ECDSA point format.

*No corpus evidence — derived from upstream documentation.*
