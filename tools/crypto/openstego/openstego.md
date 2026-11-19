---
name: "openstego"
categories:
  - blackarch-crypto
version: "0.8.6"
homepage: "https://github.com/syvaidya/openstego/releases"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-06-30"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "syvaidya/openstego"
github-stars: 1448
github-pushed: "2023-08-12"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# openstego

> Java steganography tool that hides data inside images (LSB) and applies invisible watermarks, with optional AES encryption and compression.

## What it's for

OpenStego does two jobs: data hiding — embedding an arbitrary file into a
cover image (PNG/TIFF/BMP and other formats via imageio) using LSB-based
algorithms — and watermarking — generating a signature and embedding an
invisible, password-protected watermark into images so leaked copies can be
traced back. Embedded data can optionally be compressed and encrypted
(AES128/AES256) before hiding, so a stego file reveals nothing without the
password. It ships both a GUI and a command-line interface; on BlackArch the
`openstego` command wraps `java -jar openstego.jar`, and every CLI run starts
with a subcommand (`embed`, `extract`, `gensig`, `embedmark`, `checkmark`,
`algorithms`, ...). It is not a steganalysis tool — it hides and extracts, it
does not detect third-party stego.

## Typical use cases

### List supported algorithms and formats

Discover what the installed build can do before choosing parameters.

```
openstego algorithms
openstego readformats
```

Outcome: prints the steganography algorithm names usable with `-a` (e.g. the
LSB plugin) and the supported cover/stego file formats.

### Hide a file in an image with a password

Embed a secret file into a cover image, encrypted and compressed.

```
openstego embed -mf secret.txt -cf cover.png -sf stego.png -e -c -p 'Password1'
```

Outcome: writes `stego.png`, visually near-identical to the cover, containing
the compressed, AES-encrypted contents of `secret.txt`. Without `-p` the
password is prompted for interactively; message or stego file can be `-` to
use stdin/stdout.

### Extract a hidden file

Recover the payload from a stego image.

```
openstego extract -sf stego.png -xd outdir -p 'Password1'
```

Outcome: writes the embedded file into `outdir` under its original filename
(override with `-xf`); fails cleanly if the password is wrong.

### Watermark images for leak tracing

Generate a signature and stamp an invisible watermark into images so copies
can be attributed later.

```
openstego gensig -gf sig.bin -p 'Password1'
openstego embedmark -gf sig.bin -cf photo_01.png -sf photo_01_marked.png
openstego checkmark -sf photo_01_marked.png -gf sig.bin
```

Outcome: `gensig` writes the password-derived signature file; `embedmark`
writes the watermarked image; `checkmark` reports whether the watermark is
present in a suspect copy, proving its source. With a wildcard cover such as
`'photo_*.png'` the `-sf` option is ignored and each matched cover is
overwritten in place with its watermarked version.

*No corpus evidence — derived from upstream documentation.*
