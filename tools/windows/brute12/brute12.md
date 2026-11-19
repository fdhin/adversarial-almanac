---
name: "brute12"
categories:
  - blackarch-windows
version: "1"
homepage: "http://www.security-projects.com/?Brute12"
packaged-by: [blackarch]
blackarch-builddate: "2020-12-06"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BlackArch/brute12"
github-stars: 1
github-pushed: "2020-12-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# brute12

> YJesus' 2008 Windows console cracker for PKCS#12 containers — dictionary attack on .pfx/.p12 passwords via the Windows CryptoAPI (CAPICOM).

## What it's for

Brute12 (security-projects.com, by the author of [[antiransom]]) audits the password strength of PKCS#12 cryptographic containers — the .pfx/.p12 files that bundle a certificate with its private key. It is a small console tool that walks a word list, handing each candidate to CAPICOM's Certificate.Load (the Windows CryptoAPI COM interface), so it runs on Windows or under Wine where CAPICOM is available. BlackArch packages the prebuilt brute12.exe together with its C source (brute-capi.c) under /usr/share/windows/brute12/. It is dictionary-only and takes no other options: pair it with a targeted word list rather than expecting exhaustive brute force, and treat a completed run with no hit as the audit result.

## Typical use cases

### Audit a PFX container against a word list

```
brute12.exe backup.pfx wordlist.txt
```

Outcome: tries each word (up to 30 characters) as the container password, printing a "Words tested" progress counter every 1000 guesses; if a password opens the container it prints "PKCS12 Deciphered !!" followed by the password, otherwise the run ends with "[*]End" — evidence the container survived the supplied list.

*No corpus evidence — derived from upstream documentation.*
