---
name: "pymeta"
categories:
  - blackarch-recon
version: "1.0.4.r25.gdb8a80b"
homepage: "https://github.com/m8sec/pymeta"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "m8sec/pymeta"
github-stars: 531
github-pushed: "2024-06-19"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pymeta

> Scrapes Google and Bing for documents on a domain, downloads them, and harvests exiftool metadata (authors, software, paths) into a CSV report.

## What it's for

PyMeta (a Python 3 rewrite of PowerMeta) finds OSINT in document metadata. Given a domain it builds crafted search queries for common file types (pdf, xls, xlsx, csv, doc, docx, ppt, pptx), scrapes Google and Bing for matching documents, downloads them, and extracts metadata with exiftool — surfacing usernames, naming conventions, software versions, email addresses and internal paths that feed further enumeration. It can also point at a directory of already-downloaded files. It requires `exiftool` to be installed. It is a metadata harvester, not a general search engine dorker — results are only as good as what the engines index for the domain.

## Typical use cases

### Harvest document metadata for a domain

The headline workflow in one command.

```
pymeta -d example.com
```

Outcome: scrapes Google and Bing for indexed documents on the domain, downloads them, extracts metadata with exiftool, and writes `pymeta_report.csv` (override the name with `-f`) plus a downloads directory — mine the CSV for usernames, email formats and software versions.

### Extract metadata from files you already have

Skip the scraping phase entirely.

```
pymeta -dir Downloads/
```

Outcome: walks the given directory of previously gathered documents, extracts metadata from each file, and produces the same CSV report — useful for triaging a client-supplied document dump.

### Limit scope by engine, file type, and result count

Keep the footprint small and targeted.

```
pymeta -d example.com -s google --file-type pdf,docx -m 100
```

Outcome: searches only Google, only for pdf and docx files, capped at 100 results per type — faster, lower-noise runs; `-T`, `-t` and `-j` further tune threads, timeouts and jitter.

### Route collection through a proxy

Hide or channel the search traffic.

```
pymeta -d example.com --proxy 127.0.0.1:8080
```

Outcome: sends all search and download traffic through the given proxy (or a rotating list from a file via `--proxy-file`), letting you inspect requests in Burp or distribute scraping across addresses.

No corpus evidence — derived from upstream documentation.
