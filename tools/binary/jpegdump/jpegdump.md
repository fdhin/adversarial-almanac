---
name: "jpegdump"
categories:
  - blackarch-binary
version: "0.0.7"
homepage: "https://blog.didierstevens.com/my-software/#jpegdump"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "DidierStevens/DidierStevensSuite"
github-stars: 2576
github-pushed: "2026-10-13"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---

# jpegdump

> Map the marker structure of JPEG files: segment types, offsets, lengths, appended data, embedded images.

## What it's for

jpegdump.py is Didier Stevens' JPEG structural analysis tool: it walks the
marker segments that make up a JPEG (SOI, APPn, COM, DQT, SOS, EOI, ...)
and reports each marker's file offset, type, and length. It accepts
arbitrary binary files, so it can be pointed at a JPEG with data appended
after EOI, or at a blob with complete JPEGs embedded inside it, and will
report or carve them. Like the author's other analysis tools it reads
samples directly from ZIP archives (default password `infected`). It stays
at the structure level — it does not decode pixel data.

## Typical use cases

### List the markers of a JPEG with offsets

```
jpegdump.py photo.jpg
```

Outcome: one numbered line per marker (`1 p=0x00000000 : m=ffd8 SOI`,
`2 ... m=ffe0 APP0 l=16`, ...) with offset, marker type, and segment length;
anything appended after EOI appears as a final `*trailing*` entry with its
length.

### Dump the content of a specific marker

```
jpegdump.py -s 2 -d photo.jpg
```

Outcome: prints the raw bytes of marker nr 2 (the index comes from the
listing) — useful for reading an APP or COM segment in full.

### Detect and carve embedded JPEGs from any file

```
jpegdump.py -f -e blob.bin
```

Outcome: `-f` scans for SOI markers wherever they occur and reports each
embedded JPEG found; `-e` additionally extracts those images to disk as
separate files. Add `-c` to report only compliant images.

### Analyze a sample without unpacking its malware ZIP

```
jpegdump.py sample.jpg.zip
```

Outcome: jpegdump detects the ZIP container, extracts the file in memory
(password `infected` unless overridden with `--password`), and prints its
marker list — no manual unpacking of live samples.

### Read the built-in manual

```
jpegdump.py -m
```

Outcome: prints the embedded manual covering every option and the marker
reference used by `-s`.

*No corpus evidence — derived from upstream documentation.*

GitHub: the author's actively maintained suite repository DidierStevens/DidierStevensSuite carries `jpegdump.py` at its top level (v0.0.10; the packaged zip is v0.0.7 from didierstevens.com) — his official GitHub home; the canonical distribution remains blog.didierstevens.com.
