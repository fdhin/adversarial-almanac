---
name: "oledump"
categories:
  - blackarch-binary
version: "0.0.81"
homepage: "http://blog.didierstevens.com/programs/oledump-py/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "seamustuohy/oledump"
github-stars: 6
github-pushed: "2018-11-01"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# oledump

> Analyze OLE (Compound File Binary) containers: stream inventory, VBA macro decompression, YARA and plugin triage.

## What it's for

oledump.py (Didier Stevens) dissects OLE/Compound File Binary files —
legacy Microsoft Office documents (.doc, .xls, .ppt) and many other
applications' containers — by listing every stream with its index, size,
and flags; the letter `M` marks streams containing VBA macros. From that
listing you select a stream by number and dump it (raw, hex, ASCII,
strings), decompress VBA macro source, run Python plugins or first-stage
decoders (for example an XOR brute-forcer) against a stream, and scan
streams with YARA rules. Samples stored inside (password-protected) ZIP
archives are analyzed in place with the default password `infected`, and
`oledump.py -m` prints an embedded manual. It targets the OLE era; for the
XML-in-ZIP Office formats (.docx) use ZIP-oriented tooling, and see
[[python-oletools]] for a wider OLE/macro analysis suite (olevba is the
closest counterpart).

## Typical use cases

### Inventory the streams of a legacy Office document

```
oledump.py invoice.doc
```

Outcome: numbered stream list with sizes and flags — a letter `M` next to
a stream means it contains VBA macros; that stream's index is what you
pass to `-s` next.

### Decompress the VBA macros of a flagged stream

```
oledump.py -s 7 -v invoice.doc
```

Outcome: prints the decompressed VBA source of stream 7 for review;
`-s a -v` decompresses every stream instead of a single index.

### Triage streams with YARA

```
oledump.py -y maldoc.yara invoice.doc
```

Outcome: every stream is scanned with the rule file and matches are
flagged, pointing straight at the malicious stream (YARA scanning cannot
be combined with `-s`).

### Run a plugin or decoder on a selected stream

```
oledump.py -s 3 -p plugin_http_heuristics invoice.doc
```

Outcome: the plugin — here, URL extraction from obfuscated VBA — processes
the selected stream and its findings appear alongside the dump; several
plugins can be comma-separated, and `-D decoder_xor1.py` applies a
brute-force decoder the same way.

### Analyze a sample stored in a malware ZIP

```
oledump.py sample.doc.zip
```

Outcome: oledump detects the ZIP container, decrypts it in memory with
password `infected` (override with `--password`), and analyzes the
document inside — no manual extraction of the live sample.

*No corpus evidence — derived from upstream documentation.*
