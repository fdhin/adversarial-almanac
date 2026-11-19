---
name: "tekdefense-automater"
categories:
  - blackarch-forensic
version: "88.42548cf"
homepage: "https://github.com/1aN0rmus/TekDefense-Automater"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
aliases: ["automator-git"]
meta-checked: "2026-11-09"
github-repo: "1aN0rmus/TekDefense-Automater"
github-stars: 556
github-pushed: "2024-08-13"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tekdefense-automater

> Aggregates OSINT lookups for IPs, URLs and hashes from many public sources in one run.

## What it's for

Automater (1aN0rmus / TekDefense) takes an IP address (CIDR and dash ranges accepted), URL or file hash and queries a configurable set of public web sources about it, merging the answers into a single report: reputation verdicts, geo/WHOIS data, passive DNS, hash lookups and URL unshortening. Which sources are checked and which fields are extracted is defined declaratively in the bundled `sites.xml`/`tekdefense.xml` files, so the source list can be customized without touching the Python code. Targets can be given directly or as a file of newline-separated values, and results go to the console or to text, CSV, HTML, CEF or bot-minimized output files. It is a Python 2-era tool (BlackArch wraps it for python2), and it is passive OSINT — it asks third parties about the target rather than probing the target itself.

## Typical use cases

### Look up an IP address

Baseline reputation sweep for one indicator.

```
automater 198.51.100.10
```

Outcome: queries every configured source and prints a consolidated report of what is known about the IP (reputation verdicts, geo/WHOIS, related domains, blacklistings).

### Analyze a list of indicators from a file

Batch a whole indicator set into CSV.

```
automater targets.txt -c report.csv
```

Outcome: processes each newline-separated IP/URL/hash in targets.txt in turn (2-second delay between requests by default) and writes all aggregated results to report.csv for spreadsheet or ticket import.

### Produce an HTML report for documentation

Output suitable for attaching to an assessment.

```
automater example.com -w report.html
```

Outcome: renders the lookups for the URL/domain into a formatted HTML report file; `-f report.txt` produces ArcSight CEF format instead for SIEM ingestion.

### Restrict or route the queries

Limit sources and egress path.

```
automater 198.51.100.10 -s ipvoid;robtex -d 5 --proxy proxy.example.com:8080
```

Outcome: runs only the named sources (names come from the `name` attribute in the XML config, e.g. `ipvoid`, `robtex`, `virustotal`), paces requests at 5 seconds (`-d`), and tunnels the HTTP traffic through the given proxy.

*No corpus evidence — derived from upstream documentation.*
