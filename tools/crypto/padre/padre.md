---
name: "padre"
categories:
  - blackarch-crypto
version: "v2.2.0.r15.g9a2e1f2"
homepage: "https://github.com/glebarez/padre"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-08-10"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "glebarez/padre"
github-stars: 284
github-pushed: "2025-12-12"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# padre

> Fast concurrent Go exploiter for CBC padding oracle attacks: decrypts tokens and encrypts arbitrary data through an oracle, auto-detecting block size and fingerprinting responses to confirm the vulnerability.

## What it's for

padre attacks HTTP-reachable padding oracles with heavily parallel requests. Give it a URL where the encrypted token appears — in the query string, a POST body, or a cookie, with a `$` character marking where the token sits — and it first fingerprints the HTTP responses to confirm a padding oracle exists, detects the cipher block length automatically, then decrypts the sample or mints ciphertext for plaintext you choose. It speaks Base64 (default) and lowercase hex with configurable character-replacement rules for non-standard encodings, runs a configurable number of parallel connections (default 30), and can route all traffic through a proxy such as Burp or ZAP. If an operation fails it prints hints about what to tweak. It performs the same attack as padbuster (Perl) and pax-oracle (Go) but automates more of the setup — block size and oracle detection — and is generally the fastest of the three. It does not discover oracles for you; you supply a URL and a sample token.

## Typical use cases

### Decrypt a session cookie

```
padre -u 'https://target.example.com/profile.php' -cookie 'SESS=$' 'Gw3kg8e3ej4ai9wffn%2Fd0uRqKzyaPfM2UFq%2F8dWmoW4wnyKZhx07Bg=='
```

Outcome: fingerprints responses to confirm the oracle, detects the block length, then decrypts the token and prints the recovered plaintext (for example a JSON session object such as `{"user_id": 456, "is_admin": false}`). The `$` in `-cookie` marks where each candidate token is substituted.

### Forge a token for chosen plaintext

```
padre -u 'https://target.example.com/profile.php' -cookie 'SESS=$' -enc '{"user_id": 456, "is_admin": true}'
```

Outcome: encrypts the chosen plaintext through the oracle and prints the resulting encoded ciphertext; set the cookie to that value and reload the page to test whether the application elevates the session.

### Attack a token carried in a POST parameter

```
padre -u 'https://target.example.com/api/state' -post 'data=$' 'Y2lwaGVydGV4dA%3d%3d'
```

Outcome: submits each candidate as the `data` POST parameter instead of a cookie; the Content-Type is derived automatically (override with `-ct`).

### Pin the encoding, block size, and error signature

```
padre -u 'https://target.example.com/profile.php' -cookie 'SESS=$' -err 'padding.*invalid' -e lhex -b 8 8ad9f04b11c25e7f6a0d93b4e7c2a1f8
```

Outcome: skips automatic fingerprinting and classifies responses with the `-err` regex, treats the token as lowercase hex (`-e lhex`), and pins the block length to 8 (`-b 8`; supported values 8, 16 default, 32) — the setup for a DES-family oracle with a known error page. Add `-proxy "http://localhost:8080"` to observe the traffic in Burp or ZAP.

*No corpus evidence — derived from upstream documentation.*
