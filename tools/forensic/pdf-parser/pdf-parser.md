---
name: "pdf-parser"
categories:
  - blackarch-forensic
version: "0.7.12"
homepage: "http://blog.didierstevens.com/programs/pdf-tools/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "DidierStevens/DidierStevensSuite"
github-stars: 2576
github-pushed: "2026-10-13"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---

# pdf-parser

> Parse a PDF into its indirect objects, search them and dump decoded streams — the deep-dive companion to a keyword triage scan.

## What it's for

Didier Stevens' pdf-parser decomposes a PDF document into its fundamental elements — indirect objects, streams, object streams, trailers — so you can select, filter, hash and dump them individually. It applies the common stream filters (FlateDecode, ASCIIHexDecode, ASCII85Decode, LZWDecode, RunLengthDecode), searches inside objects and streams (including regex), flags malformed elements, and can even generate a Python program that rebuilds the parsed PDF. It is the natural second step after [[pdfid]] flags a document, and pairs with [[peepdf]] when you want an interactive console instead of scripted dumps. It runs under Python 3. GitHub: the author's actively maintained suite repository DidierStevens/DidierStevensSuite carries `pdf-parser.py` at its top level (v0.7.14; the packaged zip is v0.7.12 from didierstevens.com) — his official GitHub home; the canonical distribution remains blog.didierstevens.com.

## Typical use cases

### Get a structural overview of a document

```
pdf-parser.py -a sample.pdf
```

Outcome: prints a stats overview — object counts by element type, unreferenced indirect objects, and counts of security-relevant keywords such as `/JS`, `/JavaScript` and `/OpenAction` — showing which objects are worth selecting next.

### Find every object containing a suspicious element

```
pdf-parser.py -s /JavaScript sample.pdf
```

Outcome: lists only the indirect objects (streams excluded) whose content matches the search string, each with its object id, so you can pull them individually with `-o`.

### Dump a decoded stream to disk

```
pdf-parser.py -o 12 -f -d payload.bin sample.pdf
```

Outcome: selects indirect object 12, passes its stream through the filters declared in the PDF, and writes the decoded bytes to `payload.bin` for inspection with other tooling.

### Parse objects compressed inside object streams

```
pdf-parser.py -O -a sample.pdf
```

Outcome: with `-O` the streams of `/ObjStm` objects are parsed too, so compressed objects invisible to plain keyword scans appear in the stats and can be selected with `-o`.

*No corpus evidence — derived from upstream documentation.*
