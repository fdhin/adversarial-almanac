---
name: "unblob"
categories:
  - blackarch-forensic
version: "26.6.4.r280.gcfb6c5a"
homepage: "https://github.com/onekey-sec/unblob"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "onekey-sec/unblob"
github-stars: 2636
github-pushed: "2026-11-05"
github-archived: false
github-issues: 53
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 778
gh-activity-checked: "2026-11-09"
---

# unblob

> Recursive extraction suite for unknown binary blobs and firmware images.

## What it's for

unblob identifies and unpacks 78+ archive, compression, and file-system formats (SquashFS, CPIO, UBI, ext, ZIP, 7-Zip, gzip, LZMA, and more) inside an arbitrary binary, recursing into nested containers up to a configurable depth and carving out unknown chunks as files. It is built for firmware analysis: give it a vendor firmware image and get back a directory tree of everything inside, plus entropy figures that flag encrypted or compressed regions. It extracts and carves — it does not reverse-engineer the contents of formats it cannot unpack.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Extract a firmware image

Unpack everything inside an opaque blob.

```
unblob firmware.bin
```

Outcome: creates `firmware.bin_extract/` containing all recognized chunks recursively extracted (default depth 10) plus carved unknown-chunk files, using all CPU cores.

### Choose the output directory and force re-extraction

Keep results in a known place.

```
unblob -e /tmp/output -f firmware.bin
```

Outcome: extracts into /tmp/output; `-f` re-extracts even when output files already exist.

### Generate a JSON metadata report

Document what was found where.

```
unblob --report report.json firmware.bin
```

Outcome: writes report.json with chunk offsets, sizes, entropy, ownership, permissions, and timestamps — a scriptable map of the image's layout.

### Tune recursion and entropy analysis

Balance depth against noise.

```
unblob -d 5 -n 2 firmware.bin
```

Outcome: caps recursion at five levels and computes entropy two levels deep (`-n 0` disables it), highlighting unknown chunks that are likely encrypted or compressed.

### Check external extractor availability

Verify the toolchain before a long extraction.

```
unblob --show-external-dependencies
```

Outcome: lists every external tool unblob shells out to (7z, sasquatch, lz4, ...) and whether it is installed — run this first when extraction silently skips a format.
