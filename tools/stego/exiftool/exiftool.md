---
name: "exiftool"
categories:
  - stego
homepage: "https://exiftool.org/"
origin: "hacktricks"
recipe: "upstream"
packaged-by: [arch]
build-system: perl
license: GPL-3.0
meta-checked: "2026-11-13"
github-repo: "exiftool/exiftool"
github-match: "exact"
github-stars: 5260
gh-checked: "2026-11-13"
github-commits-year: 26
gh-activity-checked: "2026-11-13"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# exiftool

> Reads, writes and strips metadata in 150+ file types — the standard first probe for hidden data in images, documents and media.

## What it's for

ExifTool is Phil Harvey's Perl library and CLI for meta information in
images, documents, audio and video — EXIF, IPTC, XMP, ICC, MakerNotes,
embedded thumbnails, plus format internals (PDF, ZIP, EXE, PCAP...). In
offensive work it is the first tool applied to any supplied file: dumped
metadata leaks author names, software versions, GPS coordinates and
sometimes credentials or hidden payloads, and writable metadata is itself
an injection channel when an application consumes uploaded files'
metadata unsafely. It also extracts embedded/preview images that may
differ from the visible one, and strips everything (`-all=`) for
anti-forensics or anonymizing exfil. Writes preserve the original as
`file_original` by default. It is a single dependency-free Perl script.

*Grounded in corpus command-line evidence (five distinct patterns: full
dump of a PDF, single-tag reads with `-s`, `-Artist` tag reads and writes,
and `-Creator -csv` exports over file sets); remaining patterns derived
from the upstream man page embedded in the `exiftool` script.*

## Typical use cases

### Dump all metadata from a file

First-look at everything a file carries.

```
exiftool document.pdf
```

Outcome: prints every readable tag grouped by metadata family (EXIF, PDF,
XMP, File...), including software versions, author/producer strings and
timestamps — the standard opening move before deeper stego analysis.

### Deep dump including unknown and duplicate tags

Surface tags the default view suppresses.

```
exiftool -a -u -g1 image.jpg
```

Outcome: prints all metadata including duplicate tags and unknown/
undocumented entries, sorted by group family 1 — catches obscure
MakerNotes and embedded-document metadata the plain dump omits (add
`-ee3 -api RequestAll=3` to force absolutely everything).

### Read one tag in machine-readable form

Extract a single value for scripting.

```
exiftool -s -s -s -Producer document.pdf
```

Outcome: prints just the tag's value (`-s` repeated three times strips
name, group and formatting), ready to feed into a shell variable or
pipeline without parsing prose output.

### Harvest a tag across a file set into CSV

Enumerate author-ish strings from many documents at once.

```
exiftool -Creator -csv *.pdf | cut -d, -f2 | sort -u
```

Outcome: one CSV row per file with its Creator tag; the pipeline reduces
it to the distinct values — a fast way to build a username list from a
pile of leaked PDFs/DOCX (same shape works for `-Author`, `-EmailAddress`
or any tag).

### Write or overwrite a tag

Plant a value in a file's metadata.

```
exiftool -Artist='octopus' image.jpg
```

Outcome: rewrites the Artist tag to the given string and saves the
pre-edit copy as `image.jpg_original`. Values are arbitrary — including
path-like or markup strings — which is how you test applications that
trust uploaded-file metadata (filenames built from tags, unsanitized
XMP echoed to pages).

### Strip all metadata

Remove every writable tag from a file.

```
exiftool -all= document.pdf
```

Outcome: deletes all metadata the format allows (keeping the original as
`_original`), producing a clean file for handoff or for isolating whether
a payload lives in metadata versus the file body.

### Extract an embedded thumbnail or preview

Pull the hidden inner image.

```
exiftool -b -ThumbnailImage image.jpg > thumbnail.jpg
```

Outcome: writes the embedded EXIF thumbnail (`-b` = raw bytes) to a file —
embedded previews are a classic stego channel because they can differ from
the visible image; `-b -JpgFromRaw -w _JFR.JPG -ext NEF -r .` batch-extracts
raw-embedded JPGs from a directory tree.

### Recursive inventory of a directory tree

CSV inventory of everything on disk.

```
exiftool -r -ext jpg -csv . > metadata_inventory.csv
```

Outcome: walks the tree recursively (`-r`), processes only `.jpg` files
(`-ext`), and writes one CSV row per file with all its tags — a
grep-able index of GPS, dates and author strings across a whole evidence
set.
