---
name: "stepic"
categories:
  - blackarch-stego
version: "0.4"
homepage: "http://domnit.org/stepic/doc/"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-03"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# stepic

> Python command-line and library tool for LSB image steganography.

## What it's for

Stepic, by Lenny Domnitser (upstream on Launchpad), hides arbitrary data in
images by slightly modifying pixel colours — changes generally imperceptible
to humans but machine-detectable. It works with RGB, RGBA or CMYK images and
refuses lossy formats: JPEG and similar compression destroy the payload, so
use PNG or other lossless carriers. It is both a `stepic` command and an
importable Python module, built on the Pillow imaging library, which makes it
a convenient choice when embedding must happen inside a Python script rather
than a shell pipeline.

## Typical use cases

### Hide a data file inside an image

Encode a payload file into a lossless cover image.

```
stepic --encode --in=cover.png --data-in=secret.txt --out=stego.png
```

Outcome: writes `stego.png`, visually identical to the cover, with the payload carried in per-pixel colour modifications; `-f FORMAT` can force the output image format when the extension does not determine it.

### Extract hidden data from an image

Decode a stepic-embedded payload back to a file.

```
stepic --decode --in=stego.png --out=recovered.txt
```

Outcome: reads the modified colours back out of `stego.png` and writes the recovered payload bytes to `recovered.txt`; the short flags `-e`/`-d` with `-i`, `-t`, `-o` are equivalent forms.

*No corpus evidence — derived from upstream documentation.*
