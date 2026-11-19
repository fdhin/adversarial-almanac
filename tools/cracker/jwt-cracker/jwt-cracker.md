---
name: "jwt-cracker"
categories:
  - blackarch-cracker
version: "23.8130879"
homepage: "https://github.com/brendan-rius/c-jwt-cracker"
packaged-by: [blackarch]
blackarch-builddate: "2022-11-09"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "brendan-rius/c-jwt-cracker"
github-stars: 2566
github-pushed: "2023-06-02"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jwt-cracker

> Multi-threaded C brute-forcer for HMAC-signed JWT secrets.

## What it's for

c-jwt-cracker (binary `jwtcrack`) exhaustively recovers the secret key of a JSON Web Token signed with an HMAC algorithm — HS256, HS384, HS512, or any HMAC the local OpenSSL supports. It generates candidate secrets directly against the token's signature, so it only succeeds when the secret is short or drawn from a small alphabet; it cannot attack asymmetric (RS/ES) tokens and has no wordlist mode, resume, or progress display. Typical purpose: demonstrating that an application signs its tokens with a guessable secret, which then lets you forge valid tokens. Note the upstream's caveat that its bundled Base64 decoder can occasionally report a wrong secret.

## Typical use cases

### Brute-force an HS256 token with default settings

Try every secret over the built-in full alphabet with no length bound.

```
jwtcrack eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U
```

Outcome: starts a multi-threaded exhaustive search across all threads/cores; on success prints the recovered secret and exits. There is no progress output — extended silence means the secret is outside the default space, so interrupt and re-run with a constrained search.

### Constrain alphabet, maximum length, and HMAC algorithm

Bound the search space when you know roughly what the secret looks like.

```
jwtcrack eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U ABCSNFabcsnf1234 5 sha256
```

Outcome: tests only combinations of the given characters up to the given length, hashed with the named OpenSSL HMAC (`sha256` = HS256, `sha384` = HS384, `sha512` = HS512; any OpenSSL HMAC name works). Weak keys of this shape fall in seconds; the printed secret can then be used to re-sign forged tokens.

*No corpus evidence — derived from upstream documentation.*
