---
name: "mat"
categories:
  - blackarch-defensive
version: "0.6.1"
homepage: "https://mat.boum.org/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "jubalh/MAT"
github-stars: 131
github-pushed: "2019-11-23"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mat

> Strips identifying metadata from documents, images and audio before you publish or share them.

## What it's for

MAT (Metadata Anonymisation Toolkit, by jvoisin) removes privacy-leaking metadata — camera EXIF, document author and company information, PDF producer strings, audio tags — from supported formats: PNG, JPEG, TIFF, OpenDocument, Office OpenXML (docx/pptx/xlsx), PDF, tar archives, MP3, Ogg Vorbis, FLAC and torrents. It ships a CLI (`mat`), a GUI (`mat-gui`) and a library (libmat). It only processes metadata: it does not anonymise file contents, handle steganography or watermarking, and it is weak on embedded media inside complex formats — images embedded in a PDF may not be cleaned. MAT is Python 2 software under maintenance hold since 2017 and upstream recommends its successor MAT2 for current systems; the historical site mat.boum.org is gone and development lives in a git repository hosted at 0xacab.org/mat/mat.

*No corpus evidence — derived from upstream documentation.*

GitHub: third-party mirror of the 0xacab.org/mat/mat upstream repository (master pinned at its 0.5.4 tag commit).

## Typical use cases

### Inspect what a file would leak

```
mat -d report.pdf
```

Outcome: lists every harmful metadata field MAT knows how to strip (author, producer, dates) without modifying the file — the pre-flight check before cleaning.

### Clean files in place

```
mat report.docx
```

Outcome: strips all harmful metadata from report.docx, replacing the original in place; add -b to keep a backup copy of the unmodified file, and pass a directory to process every supported file below it recursively.

### Verify files are clean before publishing

```
mat -c *.jpg
```

Outcome: checks each file and reports whether it is free of harmful metadata without touching it — a quick go/no-go check before upload or for scripts.

### List supported formats

```
mat -l
```

Outcome: prints every file format the installed build can clean, which depends on the optional dependencies present (mutagen for audio, exiftool for images, pdfrw/poppler for full PDF support).
