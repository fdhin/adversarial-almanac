---
name: "bulk-extractor"
categories:
  - blackarch-forensic
version: "1562.1c67a75"
homepage: "https://github.com/simsong/bulk_extractor"
packaged-by: [blackarch, kali]
blackarch-builddate: "2021-08-20"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "simsong/bulk_extractor"
github-stars: 1442
github-pushed: "2026-10-12"
github-archived: false
github-issues: 68
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 513
gh-activity-checked: "2026-11-09"
---

# bulk-extractor

> Fast multi-threaded scanner that extracts emails, URLs, credit cards and other artifacts from any data without file system parsing.

## What it's for

`bulk_extractor` (by Simson Garfinkel, originating at the US Naval
Postgraduate School) is a digital forensics triage tool that rapidly scans
any input — disk images, E01 files, individual files, memory dumps, or
directories — and extracts structured "features": email addresses, URLs,
credit card numbers, phone numbers, JSON snippets, carved JPEGs, and more.
It does not parse file systems; every byte of the input is probed by a bank
of parallel scanners, and data that decompresses or decodes (BASE64, gzip,
etc.) is recursively re-examined, so it finds evidence other carving tools
miss. Results land in per-scanner text feature files plus histograms (e.g.
of Google search terms and email addresses), which makes it the classic
first-pass tool for unknown media. It extracts, it does not interpret —
timeline reconstruction and file system analysis stay with other tools.

## Typical use cases

### Triage a disk image

Run the full scanner bank over an image into a fresh output directory.

```
bulk_extractor -o be_out image.dd
```

Outcome: creates `be_out/` containing one feature file per scanner
(`email.txt`, `url.txt`, `ccn.txt`, …), histogram files such as
`url_histogram.txt`, and a `report.xml` summarizing the run; grep the
feature files for leads before deeper analysis.

### Run a single scanner exclusively

Skip everything else and extract only one feature type — useful against
huge images.

```
bulk_extractor -o be_out -E email disk.E01
```

Outcome: disables all scanners except `email`, so the run finishes faster
and only `email.txt` and its histogram are produced.

### Tune scanners and threads

Enable or disable individual scanners and set the thread count for the
host.

```
bulk_extractor -o be_out -e email -e url -x gzip -j 4 image.dd
```

Outcome: runs with the `email` and `url` scanners forced on, `gzip`
disabled, and 4 worker threads; the effective scanner set is recorded in
`report.xml`.

### Hunt for a specific string

Search the raw data for known indicators in the same pass.

```
bulk_extractor -o be_out -f 198.51.100.10 image.dd
```

Outcome: every occurrence of the pattern (repeatable `-f`) is written to
`find.txt` with its forensic path and context, alongside the regular
scanner output.

### Scan a directory of files recursively

Point it at a folder of loose files instead of an image.

```
bulk_extractor -o be_out -R evidence_files/
```

Outcome: treats the directory as the input and recursively scans every file
in it, producing the same per-scanner feature files over the folder's
contents.

*No corpus evidence — derived from upstream documentation.*
