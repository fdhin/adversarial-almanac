---
name: "pngcheck"
categories:
  - blackarch-stego
version: "3.0.3"
homepage: "http://www.libpng.org/pub/png/apps/pngcheck.html"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-06-23"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "pnggroup/pngcheck"
github-stars: 78
github-pushed: "2025-12-15"
github-archived: false
github-issues: 13
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# pngcheck

> Validate PNG/JNG/MNG files and dump their chunk structure.

## What it's for

pngcheck, by Jason Summers, is the standard command-line validator for PNG
(and JNG/MNG) files: it verifies chunk CRCs, checks chunk ordering and sizes,
decompresses the image data to confirm it is well-formed, and prints the
metadata a file carries. It reports exactly where a file breaks when it is
corrupt, and any unusual or unexpected chunks when it is not. It is a file
format diagnostic, not a steganography extractor — but it is the natural
first triage step for a suspicious PNG, revealing anomalous chunks, appended
data or metadata worth pursuing with dedicated stego tools.

## Typical use cases

### Check a file's basic validity

Run the fast one-line integrity check.

```
pngcheck screenshot.png
```

Outcome: prints `OK: screenshot.png` with dimensions and bit depth for a valid file, or the specific CRC/structure errors that make it invalid.

### Dump the chunk structure verbosely

List every chunk with offsets and contents for detailed inspection.

```
pngcheck -v screenshot.png
```

Outcome: prints one line per chunk (IHDR, iCCP, tEXt, IDAT, ...) with offset, length and parsed contents, then decompresses the IDAT stream and reports whether errors were detected — the view to compare against a known-good file of the same origin.

### Batch-scan a directory of PNGs

Validate many downloaded or extracted files in one pass.

```
pngcheck *.png
```

Outcome: one status line per file; corrupt or suspicious files stand out as error entries while the rest pass, so follow-up can target just the failures.

*No corpus evidence — derived from upstream documentation.*
