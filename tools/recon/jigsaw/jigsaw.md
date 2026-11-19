---
name: "jigsaw"
categories:
  - recon
version: "1.3"
homepage: "https://github.com/pentestgeek/jigsaw"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "R3dy/jigsaw"
github-stars: 24
github-pushed: "2013-08-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jigsaw

> Ruby script (from the Pentestgeek/Accuvant lineage) that enumerates a company's employees and their email addresses from the old Jigsaw.com contact database — OSINT for phishing pretexts.

## What it's for

Employee enumeration against the former Jigsaw.com business-contacts service (later Salesforce Data.com, since retired): search a company name, resolve its numeric Jigsaw company ID, then pull every employee record — name, title, city/state — and craft email addresses for a chosen domain in the two standard `first.last@` / `flast@` permutations, exportable to CSV for social-engineering and phishing-list building. The data source no longer exists, so the tool is of historical/reference value while remaining packaged (ArchStrike ships it from a vendored 2014 source tarball, since the original GitHub repositories — pentestgeek/jigsaw and AccuvantLABS/jigsaw — are both gone). It does nothing without the Jigsaw backend.

## Typical use cases

### Find a company's numeric ID

```
./jigsaw.rb -s Example
```

Outcome: searches Jigsaw for the company name and, when several match, lists each candidate with its Jigsaw company ID and employee count (e.g. `215043 - Example, Inc. 6,627 employees.`). Take the ID into the next command. Searching a person's name instead (`-s "Jane Doe"`) returns matching individuals with title, company and location.

### Dump employees and craft email addresses

```
./jigsaw.rb -i 215043 -d example.com -r example
```

Outcome: pulls all employee records for company ID 215043 (page by page, with progress percentages on large companies), crafts both email permutations for each person using `-d example.com`, and writes the report to `example.csv` — name, title, both email forms, city and state per row. Add `-k "security consultant"` to keep only records whose title contains that keyword, `-P`/`-p` to route through an HTTP proxy such as Burp, and `-v`/`-D` for verbose request/response debugging.

*No corpus evidence — derived from the packaged source tarball's README (jigsaw.rb v1.5.3, vendored by ArchStrike; upstream repository deleted).*
