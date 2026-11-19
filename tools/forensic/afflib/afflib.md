---
name: "afflib"
categories:
  - blackarch-forensic
version: "3.7.22"
homepage: "https://github.com/sshock/AFFLIBv3"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-12-03"
build-system: autotools
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sshock/AFFLIBv3"
github-stars: 94
github-pushed: "2026-11-02"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# afflib

> The Advanced Forensic Format library and toolkit: compressed, signed or encrypted disk images that carry their own metadata, plus the CLI tools to inspect and convert them.

*No corpus evidence — derived from upstream documentation.*

## What it's for

AFF (Advanced Forensic Format) stores a disk image together with its forensic metadata — case identifiers, hashes, acquisition notes — in one file that can be zlib/LZMA-compressed, digitally signed for chain of custody, or stored encrypted and decrypted on the fly. The afflib package ships the reference library plus the `aff*` command-line toolkit: inspecting image metadata, converting between raw ("dd") and AFF images, verifying signatures, and FUSE-mounting an AFF image read-only for tools that expect a plain block device. [[aimage]] is the companion imager that acquires devices directly into AFF; the AFF format itself is in maintenance mode, superseded by AFF4. BSD-4-Clause licensed.

## Typical use cases

### Inspect an AFF image's metadata

See what an AFF file contains before using it.

```
affinfo evidence.aff
```

Outcome: prints the image's segments and metadata — page size, compression, embedded hashes, and any signed acquisition segments — plus bad-block accounting; used first to sanity-check an image and its chain of custody.

### Convert an AFF image to raw

Hand an AFF image to tools that only read dd images.

```
affcat evidence.aff > evidence.raw
```

Outcome: streams the image's data pages to stdout in raw format (missing pages are reported rather than padded); redirecting to a file yields a plain dd image usable with any standard forensic utility.

### Convert between raw and AFF

Wrap a raw image into AFF (or recompress an existing one).

```
affconvert disk.raw
```

Outcome: converts `disk.raw` into a compressed `disk.aff` carrying the same data in AFF form; running it on an `.aff` file recompresses or uncompresses between AFF images, and the inverse direction converts AFF back to raw. The source file's extension must be `raw` or `aff`.

### Mount an AFF image read-only

Expose an AFF image as a block device.

```
affuse evidence.aff /mnt/aff
```

Outcome: presents the AFF image's uncompressed data at the given mount point via FUSE, so file-system tools (mmls, fsstat, mount -o loop over losetup) can process the image without a full raw conversion.
