---
name: "emldump"
categories:
  - blackarch-forensic
version: "0.0.11"
homepage: "https://blog.didierstevens.com/my-software/#emldump"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/emldump"
github-stars: 1
github-pushed: "2019-05-29"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# emldump

> Index the MIME structure of an .eml file and dissect any part of it: dumps, YARA triage, obfuscation handling.

## What it's for

emldump (Didier Stevens; upstream script `emldump.py`) analyzes MIME
files — `.eml` messages and MIME-wrapped malware samples. Run without
options it prints the file's parts with an index number, size, and MIME
type, where an `M` marks a multipart container that cannot be selected
directly. From that inventory you select a part by number (or MIME
type) with `-s` and dump it as a hex dump, ASCII dump, or raw data, cut
byte ranges out of it, run first-stage decoders, or scan every part
with YARA rules. The input may be a file, stdin, or a sample inside a
(password-protected) ZIP archive. It also copes with dirty input: `-H`
skips leading lines that are not RFC 822 fields (as saved by Lotus
Notes), `-f` filters out obfuscating lines, and `-F` repairs
MIME-Version obfuscation. It ships in the Didier Stevens Suite — see
[[didier-stevens-suite]] — and is the MIME counterpart of
[[oledump]] for OLE containers and [[make-pdf]] for PDF streams.

## Typical use cases

### Inventory the parts of a message

First look at what a MIME file contains.

```
emldump sample.eml
```

Outcome: a numbered list of parts, one per line, each with its byte
size and MIME type and an `M` marker on multipart containers; the
index numbers drive every `-s` selection below.

### Hex-dump a selected part

Pull one part out for inspection.

```
emldump sample.eml -s 2
```

Outcome: a hex dump of part 2's decoded content; `-s` also accepts a
MIME type (e.g. `text/html`) instead of an index, `-a` switches to a
printable-ASCII rendering, and `-d` writes the raw bytes.

### Scan every part with YARA

Rule-based triage across the whole message.

```
emldump sample.eml -y rules.yar
```

Outcome: each part is scanned with the rule file (a directory or
`@file` of rules works too) and matches are flagged with the part
index — pointing straight at the malicious attachment (YARA scanning
cannot be combined with `-s`).

### Parse a dirty or obfuscated sample

Deal with samples that are not clean MIME.

```
emldump sample.eml -H -f
```

Outcome: leading non-field lines are skipped (a warning reports how
many) and obfuscating lines longer than 100 characters are filtered out
before parsing; add `-F` to additionally repair MIME-Version
obfuscation.

### Analyze a sample straight out of a ZIP

Handle malware distributed in (encrypted) archives.

```
emldump sample.eml.zip
```

Outcome: emldump detects the ZIP container, decrypts it in memory with
its built-in password handling, and analyzes the MIME file inside
without extracting the sample to disk.

*No corpus evidence — derived from upstream documentation.*
