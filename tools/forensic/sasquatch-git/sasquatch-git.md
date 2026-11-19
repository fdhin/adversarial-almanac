---
name: "sasquatch-git"
categories:
  - forensic
version: "20210324.r60"
homepage: "https://github.com/devttys0/sasquatch"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur, nixpkgs]
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "devttys0/sasquatch"
github-stars: 569
github-pushed: "2023-05-23"
github-archived: false
github-issues: 37
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sasquatch-git

> unsquashfs patched to extract the vendor-mangled SquashFS found in router and IoT firmware.

## What it's for

sasquatch (by Craig Heffner, devttys0 — the [[binwalk]] author) is squashfs-tools
v4.3's `unsquashfs` with patches that tolerate the vendor-specific SquashFS
variants embedded in commercial firmware (DD-WRT and other embedded vendors):
mangled header fields, altered magic/version numbers, and tweaked LZMA
compression options, which it brute-forces when autodetection fails. Stock
`unsquashfs` rejects these images; sasquatch usually extracts them. Extra flags
override endianness, version fields and compression for the stubborn cases.

## Typical use cases

### Extract a vendor SquashFS image

Unpack a firmware filesystem image that stock unsquashfs refuses.

```
sasquatch firmware.bin
```

Outcome: extracts the filesystem tree into `./squashfs-root`, silently trying
known vendor hacks and brute-forcing LZMA options where headers lie.

### Force big-endian interpretation

Handle images whose endianness flag was stripped or altered.

```
sasquatch -be firmware.bin
```

Outcome: parses all multi-byte header fields as big endian instead of trusting
autodetection — often the fix for MIPS/PowerPC vendor images.

### Override the SquashFS version fields

Extract images with forged or zeroed version numbers.

```
sasquatch -major 3 -minor 0 firmware.bin
```

Outcome: skips version autodetection and parses the image using the SquashFS
3.x on-disk layout.

### Force a specific decompressor

When compression autodetection guesses wrong.

```
sasquatch -comp lzma firmware.bin
```

Outcome: overrides the detected compression and decompresses data blocks with
the named decompressor (e.g. `lzma`, `gzip`).

*No corpus evidence — derived from upstream documentation.*
