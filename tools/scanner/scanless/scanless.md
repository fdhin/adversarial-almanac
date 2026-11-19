---
name: "scanless"
categories:
  - blackarch-scanner
version: "90.3da40e9"
homepage: "https://github.com/vesche/scanless"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "vesche/scanless"
github-stars: 1123
github-pushed: "2023-08-07"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scanless

> CLI and Python library that delegates port scans to public online scanning websites.

## What it's for

scanless uses websites that offer free online port scans (yougetsignal, viewdns, ipfingerprints, spiderip, standingtech) to scan a target from their infrastructure instead of yours — useful when you want a third-party vantage point or must not touch the target directly. It works both as a command-line utility and as an importable library returning parsed results. The project is unmaintained since August 2023, so the scraping adapters behind the scanners will break as the sites change; treat failures as drift, and only scan assets you are authorized to have scanned by third parties.

## Typical use cases

### List the available scanners

See which online services can be used.

```
scanless -l
```

Outcome: prints the scanner name/website table (ipfingerprints, spiderip, standingtech, viewdns, yougetsignal) that `-s` selects from.

### Scan a target through a chosen site

Run a port scan from a third-party vantage point.

```
scanless -t 198.51.100.10 -s viewdns
```

Outcome: submits the target to the named scanner and prints the PORT/STATE/SERVICE table it returns; omit `-s` for the default (yougetsignal), use `-r` for a random scanner or `-a` to query every scanner in sequence, and `-d` for debug output on failures.

### Use it as a library

Embed online scans in Python tooling.

```
python3 -c "import scanless, json; out = scanless.Scanless().scan('198.51.100.10', scanner='yougetsignal'); print(json.dumps(out['parsed'], indent=2))"
```

Outcome: returns a dict with `raw` (the scraped text) and `parsed` (a list of port/state/service/protocol records) for programmatic consumption.

*No corpus evidence — derived from upstream documentation (vesche/scanless README; project archived upstream since August 2023).*
