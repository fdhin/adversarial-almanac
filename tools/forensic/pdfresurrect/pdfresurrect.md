---
name: "pdfresurrect"
categories:
  - blackarch-forensic
version: "0.12"
homepage: "http://packetstormsecurity.com/files/118459/PDFResurrect-PDF-Analyzer.12.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2018-11-15"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "enferex/pdfresurrect"
github-stars: 108
github-pushed: "2022-09-10"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pdfresurrect

> Recover the previous versions hidden in a PDF's incremental-update history and write each revision out as its own file.

## What it's for

The PDF format allows earlier revisions of a document to survive inside a later one: each incremental save appends new objects and a new xref/trailer while the old bytes stay in the file. pdfresurrect (Matt Davis) locates those revisions, reports what changed between versions, and writes each previous version out as a separate file, leaving the original unmodified — turning a "cleaned" document's hidden history (redacted text, replaced payloads, earlier metadata) back into readable evidence. Caveats from the author: the per-version object-type hints are heuristic, object counts on linearized PDFs include the linearization objects, and the scrub feature is experimental. For per-object exploration of the latest version rather than history recovery, see [[peepdf]] or [[pdf-parser]].

## Typical use cases

### Review version history and creator info

```
pdfresurrect -i sample.pdf
```

Outcome: prints the PDF creator information for each revision plus a per-version summary with object counts, showing how many versions the document contains and what each contributed.

### Recover all previous versions to disk

```
pdfresurrect -w sample.pdf
```

Outcome: writes every detected version as a separate numbered file plus a summary file into the working directory; the original PDF is never modified. Open each recovered file to compare what the document looked like before later edits.

### Quickly count hidden revisions

```
pdfresurrect -q sample.pdf
```

Outcome: prints only the number of versions contained in the PDF — a fast check for whether a full extraction pass is worthwhile.

### Scrub the revision history (experimental)

```
pdfresurrect -s sample.pdf
```

Outcome: attempts to zero the previous-version data so reading applications see only the latest revision. Upstream explicitly warns this is experimental and not trustworthy for security purposes — verify the output afterwards and expect the result may no longer render.

*No corpus evidence — derived from upstream documentation.*
