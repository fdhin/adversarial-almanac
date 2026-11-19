---
name: "jefferson"
categories:
  - blackarch-forensic
version: "v0.4.8.r21.gf605cd1"
homepage: "https://github.com/onekey-sec/jefferson"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "onekey-sec/jefferson"
github-stars: 99
github-pushed: "2026-11-06"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 40
gh-activity-checked: "2026-11-09"
---

# jefferson

> Extract the contents of JFFS2 filesystem images — typical in router and IoT firmware dumps — into a directory on the host.

## What it's for

Python 3 extractor for JFFS2, the raw-NAND flash filesystem found in embedded firmware. It auto-detects big- and little-endian images, decodes zlib, rtime, LZMA and LZO compression, checks header CRCs, and recreates files, directories, symlinks and device nodes on the host. When duplicate inode numbers reveal several JFFS2 filesystems concatenated in one file (common in firmware dumps), each segment is treated as a separate filesystem. Originally written by Stefan Viehböck (sviehb) and now maintained by ONEKEY, it is the standard JFFS2 stage in firmware analysis pipelines (frequently driven behind extraction frameworks such as unblob). It unpacks to a directory; it does not mount images, and it handles only JFFS2 — other filesystem types in a firmware dump need their own extractors.

## Typical use cases

### Extract a JFFS2 image into a target directory

Unpack a carved JFFS2 segment from a firmware dump.

```
jefferson filesystem.img -d outdir
```

Outcome: the directory is created and populated with the recovered filesystem tree (files, directories, symlinks, device nodes); progress and per-segment extraction are logged to the console.

### Extract to the default directory

Run without `-d` and let the tool pick the destination.

```
jefferson firmware.bin
```

Outcome: extraction lands in `./jffs2-root` in the current working directory.

### Re-extract over an existing destination

Overwrite a previous extraction run in the same output directory.

```
jefferson filesystem.img -d outdir -f
```

Outcome: existing outputs in `outdir` are removed and extraction proceeds instead of aborting with "Destination path already exists!"; add `-vvv` for maximum detail on what is decoded.

*No corpus evidence — derived from upstream documentation.*
