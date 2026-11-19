---
name: "binwalk"
categories:
  - forensics
  - hardware
version: "3.1.0-1"
homepage: "https://github.com/ReFirmLabs/binwalk"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-10-31"
build-system: go
meta-checked: "2026-11-09"
github-repo: "ReFirmLabs/binwalk"
github-stars: 14628
github-pushed: "2026-08-11"
github-archived: false
github-issues: 103
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# binwalk

> Firmware and binary-blob analysis: signature-scans embedded files out of an image and can extract them, with entropy analysis for hidden compression or encryption.

## What it's for

binwalk identifies (and optionally extracts) files and data embedded inside other files — filesystems, compressed archives, kernels and code hidden inside firmware images or seemingly plain blobs. Its primary focus is firmware analysis: point it at a vendor image and it reports every embedded object by offset, then can carve and unpack them recursively. Entropy graphing flags encrypted or packed regions. The 3.x series is a Rust rewrite with a reduced command set: the old 2.x options `-A`/`--opcodes` (opcode scanning), `--dd` (carve by signature regex, e.g. `--dd='.*'` to carve everything) and `--run-as` (privilege-dropping during extraction) no longer exist — extraction is now done with `-e`/`--extract`. It is not a hex editor or a carver for files with no signature match.

## Typical use cases

### Signature-scan a firmware image

Find out what is really inside a binary blob.

```
binwalk firmware.bin
```

Outcome: prints a table of every recognized signature with its decimal/hex offset and description — embedded filesystems, kernels, certificates, compressed streams. A file that "looks like just data" often reveals a hidden executable this way.

### Extract embedded file systems and data

Unpack everything binwalk recognizes.

```
binwalk -e firmware.bin
```

Outcome: known file types are automatically carved and decompressed into the extraction directory (`extractions` by default); binaries with missing decompression utilities report an extraction error while the scan still succeeds. This is the standard first move on a firmware image.

### Extract recursively into a chosen directory

Follow nested containers during extraction.

```
binwalk -Me -d extracted firmware.bin
```

Outcome: `-M` (matryoshka) re-scans each extracted file and keeps unpacking nested layers; `-d` redirects output to `extracted/` instead of the default directory.

### Generate an entropy graph

Spot encrypted or packed regions.

```
binwalk -E firmware.bin
```

Outcome: computes and plots per-block entropy for the file; high sustained entropy indicates compression or encryption, while dips and plateaus hint at embedded plaintext structures. Mutually exclusive with `-e`.

### List supported signatures and extractors

Check what binwalk can recognize before scanning.

```
binwalk -L
```

Outcome: prints the built-in signature and extractor list, which also provides the exact signature names accepted by the `-y`/include and `-x`/exclude filters.

### Carve known and unknown content to a directory

Extract everything, including data with no recognized signature.

```
binwalk --carve -d carved firmware.bin
```

Outcome: writes both recognized file types and unknown data regions to `carved/` as separate files — unlike `-e`, which only extracts known types. This is the v3 replacement for the old 2.x `--dd='.*'` carve-everything invocation; it works with or without `--extract` and combines with `-M` for recursive runs.

### Save scan results as JSON

Keep a machine-readable record of the analysis.

```
binwalk -e firmware.bin -l results.json
```

Outcome: every signature hit (offset, description, extraction result) is written to `results.json` while the normal table still prints — feed it to scripts, or diff two firmware images by offset. Pass `-l -` to log to stdout instead of a file; entropy analysis results (`-E`) are logged the same way.
