---
name: "panhunt"
categories:
  - blackarch-scanner
version: "63.ec87e88"
homepage: "https://github.com/Dionach/PANhunt"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Dionach/PANhunt"
github-stars: 159
github-pushed: "2023-04-13"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# panhunt

> Searches drives and mail stores for credit card numbers (PANs) — PCI DSS scoping made simple.

## What it's for

PANhunt (Dionach) hunts Primary Account Numbers — Visa, MasterCard and AMEX card numbers — across the documents on a drive: office and text formats (.doc, .xls, .xml, .txt, .csv), Office XML/zip containers (.docx, .xlsx, .zip, recursed into their XML parts), Outlook .msg/.pst mail stores including attachments, and it lists (but does not yet search) Access databases and other binary formats. It exists for PCI DSS scope verification: proving where card data actually lives before an assessor looks. Output is a timestamped text report of matched files with masked, per-scheme PANs. Written Windows-first (defaults scan `C:\` excluding system directories, so pass `-s`/`-x` explicitly elsewhere); the packaged console is `panhunt`.

## Typical use cases

### Sweep a mounted drive for card numbers

Find every document holding PANs on a share or imaged drive.

```
panhunt -s /mnt/case-drive -o pans_report.txt
```

Outcome: walks the tree with progress bars, opens the configured document formats, recurses zip containers, parses mail files, and writes a report listing each file with masked matches per card scheme (e.g. `Visa:************1111`); by default the report is `panhunt_<timestamp>.txt`.

### Unmask matches and filter a known test PAN

Show full card numbers for evidence while dropping a test card from results.

```
panhunt -s /mnt/case-drive -u -X 4111111111111111
```

Outcome: same sweep but PANs print unmasked (`-u`), and the given PAN (typically a known test card) is excluded from matches via `-X`.

### Scope which file types get searched

Tune the extension lists for the target environment.

```
panhunt -s /srv/exports -t .txt,.csv,.log -z .zip
```

Outcome: only the given text extensions and zip containers are opened and parsed — faster on huge shares where only specific formats matter. `-m` adjusts mail extensions (.pst default), `-l` the listed-not-searched formats.

No corpus evidence — derived from upstream documentation (Dionach/PANhunt README usage block).
