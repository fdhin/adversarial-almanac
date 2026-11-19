---
name: "matroschka"
categories:
  - blackarch-stego
version: "58.2f026a4"
homepage: "https://github.com/fgrimme/Matroschka"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "fgrimme/Matroschka"
github-stars: 435
github-pushed: "2019-11-09"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# matroschka

> Hide encrypted text or images in the least significant bits of PNG/BMP images.

## What it's for

Matroschka is a command-line steganography tool in pure Python that hides a
secret (a `.txt` file or a `.png` image) in the least significant bits of a
carrier image's pixels. Hidden data is encrypted with XTEA in CFB mode and
authenticated with HMAC-SHA256, keyed by two separate user-supplied passwords
(one for the message key, one for the MAC). Only lossless formats work — the
upstream recommends PNG or BMP carriers, and transparent pixels can show
visible artifacts after embedding. The author states plainly that it is a fun
project and not for serious encryption needs.

## Typical use cases

### Hide a secret in a carrier image

Embed and encrypt a text or PNG secret using both passwords.

```
python matroschka.py -hide -m Password1 -k Password2 secret.txt cover.png
```

Outcome: rewrites the carrier with the secret embedded in its pixel LSBs; the output looks like the original image but carries the encrypted payload.

### Reveal a hidden secret

Extract and decrypt a previously embedded secret from a stego image.

```
python matroschka.py -open -m Password1 -k Password2 cover.png
```

Outcome: decrypts and authenticates the hidden data; an embedded text is printed, an embedded image is saved (e.g. as `secret-image.png`). A wrong password or tampered image fails authentication.

*No corpus evidence — derived from upstream documentation.*
