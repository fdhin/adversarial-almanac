---
name: "imagegrep"
categories:
  - blackarch-misc
version: "8.1f14af2"
homepage: "https://github.com/coderofsalvation/imagegrep-bash"
packaged-by: [blackarch]
blackarch-builddate: "2020-06-07"
build-system: scripts
license: LGPL
meta-checked: "2026-11-09"
github-repo: "coderofsalvation/imagegrep-bash"
github-stars: 7
github-pushed: "2020-05-28"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# imagegrep

> OCR-backed grep: test whether a word appears inside a PDF or image.

*No corpus evidence — derived from upstream documentation.*

## What it's for

imagegrep (from coderofsalvation/imagegrep-bash) applies OCR to a PDF or image and reports whether a given word occurs in the recognized text — a grep for content that was never machine-readable text to begin with. Arguments are the file, the word and the OCR language (a tesseract code such as `eng`), and the exit status reflects the match, so it composes with `&&`, `||` and read-loops to sort scanned documents by content. It drives tesseract and ImageMagick, which must be installed. OCR is lossy: imagegrep answers "does this word very likely appear", which is exactly right for triage and exactly wrong for dependable full-text extraction.

## Typical use cases

### Test a single scanned document for a keyword

Check one PDF for a word before filing it away.

```
imagegrep invoice.pdf invoice eng
```

Outcome: runs English-language OCR over `invoice.pdf` and exits 0 when the word `invoice` is found in the recognized text (non-zero otherwise) — chain `&& echo found` for a visible verdict on the command line.

### Sort a folder of scans by content

Route each file to a destination based on what the OCR layer sees.

```
find scans/ -name '*.pdf' | while read -r file; do
    imagegrep "$file" invoice eng && mv "$file" invoices/
done
```

Outcome: every PDF whose OCR text contains "invoice" is moved into `invoices/`; the rest stay put. Extend the loop with more branches (for example `|| imagegrep "$file" receipt eng`) to build a multi-way sorter.
