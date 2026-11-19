---
name: "rsakeyfind"
categories:
  - blackarch-cracker
version: "1.0"
homepage: "http://citp.princeton.edu/memory/code/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2018-11-29"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "congwang/rsakeyfind"
github-stars: 19
github-pushed: "2017-08-27"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rsakeyfind

> Locates BER-encoded RSA keys in a raw memory image, as used in cold-boot key recovery.

## What it's for

RSAKeyFinder 1.0 by Nadia Heninger and J. Alex Halderman (Princeton CITP),
the reference tool accompanying "Lest We Remember: Cold-Boot Attacks on
Encryption Keys" (USENIX Security 2008). It scans a captured RAM image for
RSA keys held in standard BER/DER encoding — the format several
implementations of that era (e.g. Apache 2.2 with mod_ssl) kept in memory.
Two modes: given a hex-encoded modulus it searches for that specific key
(private and public); given only the image it searches for a fixed BER
version-marker pattern and parses any private keys found around it. Keys
held in memory in any other format are not detected.

## Typical use cases

### Find any RSA private keys in a memory dump

Pass only the memory image; the tool searches for the BER version marker.

```
rsakeyfind memory.img
```

Outcome: prints `FOUND PRIVATE KEY AT <hex-offset>` followed by the decoded
key fields for every parseable private key, showing which secrets a
captured image still contains.

### Find the key matching a known public modulus

Supply a second file containing the hex-encoded modulus of a known public
key (whitespace is ignored).

```
rsakeyfind memory.img modulus.hex
```

Outcome: prints FOUND PRIVATE/FOUND PUBLIC KEY lines with offsets only for
keys whose modulus matches, confirming whether a specific certificate's
private key is present in the image.

*No corpus evidence — derived from upstream documentation.*
