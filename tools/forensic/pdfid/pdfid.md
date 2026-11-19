---
name: "pdfid"
categories:
  - blackarch-forensic
version: "0.2.10"
homepage: "http://blog.didierstevens.com/programs/pdf-tools/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Rafiot/pdfid"
github-stars: 55
github-pushed: "2016-05-27"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pdfid

> Fast keyword triage for PDFs: counts of `/JavaScript`, `/OpenAction`, `/Launch` and friends — plus scoring plugins — before any deep parsing.

## What it's for

PDFiD is Didier Stevens' first-look PDF triage tool. Rather than fully parsing a document (complex and historically bug-prone), it scans the raw bytes for a fixed list of PDF keywords and reports total and obfuscated occurrences of each, so active content — JavaScript, automatic actions, launch commands, embedded files — stands out immediately. It is deliberately simple by design, accepts single files, wildcards, ZIP archives, URLs and whole directories, and ships scoring plugins (`plugin_triage`, `plugin_embeddedfile`, `plugin_nameobfuscation`) for batch verdicts. Anything it flags then goes to [[pdf-parser]] for object-level dumps or [[peepdf]] for interactive analysis. The tool ships from the author's site; the GitHub repository linked here is a third-party mirror of the same program.

## Typical use cases

### Triage a single document

```
pdfid.py sample.pdf
```

Outcome: prints the PDF header version followed by a count table (`obj`, `stream`, `/JS`, `/JavaScript`, `/OpenAction`, `/Launch`, `/EmbeddedFile`, ...); non-zero counts on the active-content rows mark the document for deeper analysis.

### Score a whole directory of documents

```
pdfid.py -s -p plugin_triage.py /samples
```

Outcome: `-s` scans the directory and the triage plugin appends a 0.00–1.00 score with a verdict line per file (e.g. "Sample is likely malicious and requires further analysis"); `-n` suppresses all-zero keyword rows so only interesting files clutter the output.

### Disarm active content before opening

```
pdfid.py -d sample.pdf
```

Outcome: writes `sample.disarmed.pdf` with JavaScript and auto-launch actions disabled — safe to open for viewing — leaving the original file untouched.

### Scan a file with a broken header

```
pdfid.py -f sample.bin
```

Outcome: forces the scan even without a proper `%PDF` header, useful for truncated downloads or files with junk prepended; keyword counts still reflect what the bytes contain.

*No corpus evidence — derived from upstream documentation.*
