---
name: "padbuster"
categories:
  - blackarch-crypto
version: "11.50e4a3e"
homepage: "http://www.gdssecurity.com/l/t.php"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "strozfriedberg/PadBuster"
github-stars: 813
github-pushed: "2024-07-13"
github-archived: false
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# padbuster

> Perl script that automates padding oracle attacks against CBC-mode encryption: decrypt any ciphertext the oracle will process, and forge new ciphertext that decrypts to plaintext you choose.

## What it's for

When a web application decrypts a value you supply — a cookie, query parameter, or POST field — and leaks through an error message, status code, or response difference whether the padding was valid, that service is a padding oracle. PadBuster (Brian Holyfield, Gotham Digital Science) turns that side channel into block-by-block plaintext recovery without the key, and can run the attack in reverse to produce ciphertext for any plaintext you choose, which is how encrypted session tokens get forged into privilege escalation. One invocation takes the URL, the encrypted sample, and the cipher block size (8 for DES/3DES, 16 for AES); it handles Base64, HEX, .NET UrlToken and web-safe Base64 sample encodings, and follows cookies, POST data, custom headers, proxies, and HTTP auth. It does not find oracles for you — point it at a request/response pair you already control. Newer Go implementations (padre, pax-oracle) automate more of the fingerprinting, but PadBuster remains the canonical original.

## Typical use cases

### Decrypt an encrypted cookie value

```
padbuster https://target.example.com/profile.php R2F1UzRlIX9cZXBwJm5LdA%3d%3d 8 -cookies "SESSION=R2F1UzRlIX9cZXBwJm5LdA%3d%3d"
```

Outcome: probes the oracle with modified ciphertext blocks, printing each recovered block as it falls ("decrypted block 0: ..."), and finishes with the full plaintext of the sample — showing what the server actually stores in the session cookie. Block size 8 matches DES-family ciphers; use 16 for AES.

### Forge ciphertext for a chosen plaintext

```
padbuster https://target.example.com/profile.php R2F1UzRlIX9cZXBwJm5LdA%3d%3d 8 -plaintext "user=admin" -cookies "SESSION=R2F1UzRlIX9cZXBwJm5LdA%3d%3d"
```

Outcome: runs the attack in reverse and prints new ciphertext (encoded like the sample) that the server decrypts to `user=admin`; swap it into the cookie and re-request the page to see whether the application trusts the decrypted content. Unless the IV is under your control, the first block decrypts to garbage — the useful plaintext starts at block two.

### Classify responses with an explicit error string and encoding

```
padbuster https://target.example.com/decrypt.jsp 4A5B6C7D8E9F0A1B2C3D4E5F60718293 16 -error "PaddingException" -encoding 2
```

Outcome: uses the `-error` string to tell valid from invalid padding instead of the automated response analysis, and treats the sample as uppercase hex (`-encoding 2`; 0=Base64 default, 1=lower hex, 3=.NET UrlToken, 4=web-safe Base64) — the right setup when the oracle's error page is already known and the token is not Base64.

### Recover the first block when the sample has no IV

```
padbuster https://target.example.com/profile.php R2F1UzRlIX9cZXBwJm5LdA%3d%3d 8 -noiv -bruteforce -cookies "SESSION=R2F1UzRlIX9cZXBwJm5LdA%3d%3d"
```

Outcome: `-noiv` declares that the sample's first block is real ciphertext rather than a prepended IV, and `-bruteforce` brute-forces that first block so the entire plaintext — including the leading block the basic attack cannot touch — is recovered.
