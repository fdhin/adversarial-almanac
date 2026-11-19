---
name: "origami-pdf"
gentoo-category: "app-forensics"
version: "1.2.6"
homepage: "https://code.google.com/p/origami-pdf/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: ruby
license: GPL-3
meta-checked: "2026-11-09"
github-repo: "gdelugre/origami"
github-stars: 64
github-pushed: "2014-03-25"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# origami-pdf

> Origami — a pure-Ruby PDF framework for parsing, analyzing, forging and decrypting documents, with a per-task CLI and an interactive PDF shell.

## What it's for

Origami (Guillaume Delugré, published as the Ruby gem `origami`; v1.2.6 is packaged here) parses PDF documents down to their objects, can modify and re-save them, and builds new PDFs from code. It covers the awkward corners of the format: compression filters with predictors, RC4 and AES encryption including the undocumented revision 6 key derivation, digital signatures and usage rights, attachments, AcroForm/XFA forms and object streams; it also reads FDF and PPKLite files. For analysis the gem ships task commands — `pdfextract`, `pdfcop`, `pdfdecrypt`, `pdfmetadata`, the `pdfwalker` GUI explorer — and `pdfsh`, an interactive Ruby (irb) console with the whole framework preloaded, which is the analysis console for anything the fixed commands don't cover. The project started at Sogeti's ESEC-Lab on Google Code and now lives at gdelugre/origami, where the 2.x series continues; the 1.2.x line here is the last of the first series, with the same `bin/` toolset.

## Typical use cases

### Triage a suspicious PDF

Run the filtering engine under a strictness policy.

```
pdfcop -p paranoid sample.pdf
```

Outcome: the document is scanned for malicious structures and findings are printed (colored by default, `-n` to suppress); policies range from `none` to `standard`, `strong` and `paranoid`, `-c` loads a custom policy file and `-o` writes the report to a log file.

### Extract embedded content

Pull scripts and attachments out of a document.

```
pdfextract sample.pdf -j -a -d extracted
```

Outcome: JavaScript (`-j`) and file attachments (`-a`) are written decoded into `extracted/`; the same tool extracts all decoded streams (`-s`), embedded images (`-i`), fonts (`-f`) and metadata streams (`-m`) — the usual first look inside a weaponized file.

### Decrypt an encrypted document

Produce a password-free copy for other tooling.

```
pdfdecrypt sample.pdf -p Password1 -o decrypted.pdf
```

Outcome: a decrypted copy written to `decrypted.pdf` — RC4 40–128 bits, AES128 and AES256 are supported — so tools without encryption support can process it.

### Read the metadata

```
pdfmetadata sample.pdf -i -x
```

Outcome: prints the document information dictionary (`-i`) and the XMP metadata stream (`-x`) — author, producer and timestamp fields useful for attribution and timeline work.

### Analyze interactively in the console

```
pdfsh
```

Outcome: opens an irb session with Origami preloaded ("Welcome to the PDF shell" banner, tab completion on); `pdf = Origami::PDF.read "sample.pdf"` returns a parsed document to walk and probe — `pdf.pages.size`, `pdf.each_page { |page| ... }` — decrypt, edit and re-save, using the full framework interactively.

### Parse PDFs from Ruby

The library is the primary interface.

```ruby
require 'origami'
pdf = Origami::PDF.read "sample.pdf"
puts "This document has #{pdf.pages.size} page(s)"
pdf.each_page { |page| puts page.fonts }
```

Outcome: a scriptable parser for batch triage — page count and each page's font resources printed here; the same iteration reaches streams, dictionaries and resources for anything the fixed commands don't cover.

*No corpus evidence — derived from upstream documentation.*
