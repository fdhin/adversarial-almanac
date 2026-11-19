---
name: "decodify"
categories:
  - blackarch-crypto
version: "63.4f82883"
homepage: "https://github.com/UltimateHackers/Decodify"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "UltimateHackers/Decodify"
github-stars: 933
github-pushed: "2025-09-16"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# decodify

> Recursively detects and peels chained encodings off a single string.

## What it's for

Decodify takes a string that has been encoded several times over — Base64, then hex, then Base64 again — and automatically detects each layer, decoding recursively until nothing recognizable is left. It understands Caesar/ROT, hex, decimal, binary, Base64, URL encoding and FromChar, and can look MD5/SHA1/SHA2 hashes up through third-party web services (suppress that with `-s` when the data is sensitive). Reach for it when triaging an obfuscated blob of unknown provenance; it works on encodings and hash lookups only, not on keyed ciphers. Upstream installs the binary as `dcode`; the BlackArch package installs the same script as `decodify`.

## Typical use cases

### Decode a recursively encoded string

```
dcode NjM3YTQyNzQ1YTQ0NGUzMg==
```

Outcome: prints each layer it recognizes with its decoded value (here Base64 → hex → Base64), finishing with the innermost plaintext.

### Skip online hash lookups for sensitive data

```
dcode -s NjM3YTQyNzQ1YTQ0NGUzMg==
```

Outcome: identical recursive decoding, but the MD5/SHA1/SHA2 web-lookup stage is suppressed so nothing leaves the machine.

### Brute-force Caesar offsets

```
dcode -rot 26 "bpgkta xh qtiitg iwpc sr"
```

Outcome: rotates the string through offsets 1–26 and prints every candidate that yields readable text, each tagged with its offset (`Decoded using ROT<n>`).

### Reverse a string, then decode it

```
dcode -rev 637a42745a444e32
```

Outcome: prints the reversed string first, then continues the automatic recursive decoding on the result.

*No corpus evidence — derived from upstream documentation.*
