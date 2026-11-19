---
name: "pdfgrab"
categories:
  - blackarch-recon
version: "15.1327508"
homepage: "https://github.com/c0decave/pdfgrab"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "c0decave/pdfgrab"
github-stars: 3
github-pushed: "2019-11-07"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pdfgrab

> Google-harvests the PDFs on a target's site, downloads them and dumps their metadata — Producer, Creator, Author, dates — for software-version and username leaks.

## What it's for

pdfgrab is a Python 3 reconnaissance tool for PDF metadata: point it at a single PDF URL, a local file or directory, or let its integrated Google search find the PDFs published under a target domain, and it downloads and parses each one for document metadata. The findings feed classic recon: /Producer and /Creator reveal the software (and often version) used to create documents, sometimes the rendering engine behind re-uploads; /Author and Title frequently carry real usernames and job titles; creation/modification dates add timeline context. The tool writes txt, JSON and HTML reports of the analysis by default (each individually disableable) and can regenerate an HTML report from a saved JSON. Its author is explicit that it only does the basics — for in-depth or malicious-PDF analysis, Didier Stevens' PDF tooling is the recommended next step. Version 0.4.9 is a 2019-era revival of an older tool; the Google-search mode depends on scraping and may need retries or a proxy in practice.

## Typical use cases

### Analyze a PDF by URL

Grab and dissect a single document you already know about.

```
python3 pdfgrab.py -u https://target.example.com/docs/report.pdf
```

Outcome: the PDF downloads into the working directory and its metadata prints inline — lines like `/Producer pdfTeX-0.14h`, `/Creator TeX`, `/CreationDate D:20040714015300` — with the same data written to the txt/json reports.

### Harvest a target's PDFs through Google

Let the built-in search find and process site documents in bulk.

```
python3 pdfgrab.py -s target.example.com -sn 20
```

Outcome: pdfgrab searches Google for PDFs under the given domain (multiple `-s` domains are accepted), downloads up to `-sn` results, analyzes each for metadata, and writes the consolidated reports — a per-target dossier of software and author leaks.

### Analyze PDFs already on disk

Process a single file or a whole directory of collected documents.

```
python3 pdfgrab.py -f ./downloads/report.pdf
python3 pdfgrab.py -F ./downloads
```

Outcome: the local PDF (or every PDF under the directory) is parsed for the same metadata fields without any network fetch — useful for documents gathered by other means; add `-ghr` to (re)generate the HTML report from a saved `pdfgrab_analysis.json`.

> No corpus evidence — derived from upstream documentation.
