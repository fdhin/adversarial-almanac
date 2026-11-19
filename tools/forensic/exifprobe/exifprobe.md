---
name: "exifprobe"
categories:
  - forensics
version: "2.0.1+git20201230.eee65ff-3"
homepage: "https://github.com/hfiguiere/exifprobe"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, nixpkgs]
build-system: make
meta-checked: "2026-11-09"
github-repo: "hfiguiere/exifprobe"
github-stars: 13
github-pushed: "2022-07-11"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# exifprobe

> Structured dumps of EXIF and maker-note metadata from JPEG/TIFF images and digital-camera RAW files.

## What it's for

exifprobe (originally by Duane H. Hesser, maintained as a Debian/Kali
fork by Hubert Figuière) reads the metadata inside digital images —
EXIF in JPEG and TIFF, plus RAW-family formats and maker notes for
many vendors (Canon incl. CIFF/CRW, Nikon, Fuji, Minolta, Olympus,
Panasonic, Pentax, Sigma, Sony) — and prints every tag it understands
with the structure around it: section/segment/IFD nesting, file
offsets, tag numbers, and values. Output layout is selectable:
structure mode with offsets (default), terse report mode with tag
names and values only, or flat list mode built for grep pipelines. It
is an inspection tool for metadata work that wants to know where in
the file each value lives. The upstream authors warn the code has had
no security review — do not run it as root on untrusted files. The
package also ships `exifgrep`, a wrapper that runs egrep over
exifprobe's list-mode output.

## Typical use cases

### Dump an image's metadata structure

Full-fidelity first look at a file.

```
exifprobe photo.jpg
```

Outcome: every metadata section, segment, and IFD entry is printed
with its offset in the file — the complete structural map of the
image's metadata.

### Get a readable summary of a RAW file

Values only, no offsets.

```
exifprobe -R photo.nef
```

Outcome: tag names and values, indented and inline — a compact report
of camera, lens, and shooting settings readable without parsing
offsets; structure mode (`-S`) is the explicit opposite.

### List tags across many files for grep

One tag per line over a whole directory.

```
exifprobe -L *.jpg
```

Outcome: flat list mode — each tag on its own line with the filename
at line start — so `grep`, `sort`, and friends can inventory metadata
across a collection (e.g. find every file with a GPS tag).

### Grep metadata with the companion script

Filter list-mode output through egrep in one step.

```
exifgrep 'Canon' *.jpg
```

Outcome: exifprobe's list-mode output is massaged and filtered through
egrep so only matching tag lines survive — here the Canon maker-note
entries; extra egrep options and a `NOT` pattern are passed through.

*No corpus evidence — derived from upstream documentation.*
