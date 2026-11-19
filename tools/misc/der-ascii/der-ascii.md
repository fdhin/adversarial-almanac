---
name: "der-ascii"
categories:
  - blackarch-misc
version: "108.2e87867"
homepage: "https://github.com/google/der-ascii"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "google/der-ascii"
github-stars: 252
github-pushed: "2026-10-27"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# der-ascii

> A reversible, human-editable text form of DER/BER with converters both ways, built for crafting and mutating ASN.1 structures.

## What it's for

DER ASCII is a small language for describing ASN.1 structures — including deliberately malformed ones — together with two converters: `der2ascii` turns binary DER/BER into the text form, and `ascii2der` assembles the text back into bytes. The intended workflow is round-tripping: dump a real certificate or protocol message, edit the text (change a value, break a length, insert an invalid element), and reassemble — every length prefix is recomputed for you, which hand-editing binary DER never allows. As a bonus it doubles as a readable dump for otherwise opaque DER blobs. It is purely a byte-string ⇄ text transformer for X.509/ASN.1 work: no network, no keys, no parsing of anything else.

## Typical use cases

### Dump a binary DER file to readable text

```
der2ascii -i cert.der -o cert.txt
```

Outcome: cert.txt contains the DER ASCII rendering of the structure — a commented, human-readable view you can edit directly.

### Dump a PEM file directly

```
der2ascii -pem -i cert.pem -o cert.txt
```

Outcome: the first PEM block is base64-decoded and dumped in one step, without a separate openssl conversion.

### Parse a hex dump

```
echo 30 03 02 01 05 | der2ascii -hex
```

Outcome: with `-hex` the input is read as hex (punctuation and whitespace ignored), printing the structure — here a SEQUENCE containing INTEGER 5.

### Reassemble edited text into DER

```
ascii2der -i cert.txt -o cert2.der
```

Outcome: writes the binary DER encoding with all length prefixes recomputed; feed cert2.der to the parser under test.

### Reassemble as a PEM block

```
ascii2der -i cert.txt -pem CERTIFICATE -o cert2.pem
```

Outcome: the assembled bytes are wrapped in a `-----BEGIN CERTIFICATE-----` PEM block of the requested type.

*No corpus evidence — derived from upstream documentation.*
