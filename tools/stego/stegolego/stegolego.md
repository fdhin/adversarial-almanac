---
name: "stegolego"
categories:
  - blackarch-stego
version: "8.85354f6"
homepage: "https://github.com/razc411/StegoLeggo"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-18"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "razc411/StegoLeggo"
github-stars: 5
github-pushed: "2015-11-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stegolego

> Minimal C tool that hides data in BMP images with plain LSB encoding.

## What it's for

StegoLeggo (upstream title "CovertData") is a small C program by razc411 that
hides data within BMP images using least-significant-bit encoding — nothing
more: no encryption, no compression, no password. Each BMP can carry a payload
of at most its pixel count divided by 8 bytes. The upstream builds the binary
as `covertdata`; the packaged BlackArch build compiles the same sources to
`stegolego`. Useful as the simplest possible LSB check: if data was hidden
with it, `decode` recovers it with no secrets to guess.

## Typical use cases

### Encode data into a BMP

Hide a payload string in the pixel LSBs of a BMP image.

```
stegolego encode cover.bmp "hidden payload text"
```

Outcome: writes the payload into the carrier's LSBs; the image looks unchanged but now carries the data (fails if the payload exceeds the pixel-count/8 byte capacity).

### Decode data from a BMP

Recover a previously hidden payload.

```
stegolego decode cover.bmp recovered
```

Outcome: extracts the hidden data, prints the original file name recorded at encode time, and writes the payload to the named output.

*No corpus evidence — derived from upstream documentation.*
