---
name: "make-pdf"
categories:
  - blackarch-forensic
version: "0.1.7"
homepage: "http://blog.didierstevens.com/programs/pdf-tools/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
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

# make-pdf

> Didier Stevens' generator for PDF documents with embedded JavaScript (shipped as make-pdf-javascript).

## What it's for

The `make-pdf` package installs Didier Stevens' make-pdf-javascript.py (2008, public domain, Python 2 era) as `/usr/bin/make-pdf`: it creates a PDF whose embedded JavaScript executes automatically when the document is opened in a JavaScript-enabled viewer such as Adobe Reader. It is a testing tool — for exercising PDF-handling pipelines, AV/IPS detection of active content, and learning how PDF malware is assembled — not a general PDF authoring library. By default it embeds a harmless `app.alert` message box; supply your own JavaScript inline or from a file. The upstream zip also carries sibling generators make-pdf-embedded.py (embeds files) and make-pdf-helloworld.py, and pairs naturally with Didier's analyzers: [[pdfid]] to triage a PDF's structure and [[emldump]] to dissect mail-carried attachments.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Generate a PDF that pops a message box on open

```
make-pdf test.pdf
```

Outcome: writes test.pdf containing OpenAction JavaScript that displays an `app.alert` message box when the document is opened in a JavaScript-enabled viewer.

### Embed custom JavaScript inline

```
make-pdf -j 'app.alert("Security test")' test.pdf
```

Outcome: writes test.pdf with the supplied JavaScript executed on document open — test active-content handling without a real payload.

### Embed JavaScript from a file

```
make-pdf -f payload.js test.pdf
```

Outcome: reads payload.js, embeds its contents as the auto-running script, and writes test.pdf.
