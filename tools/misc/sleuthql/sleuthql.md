---
name: "sleuthql"
categories:
  - blackarch-misc
version: "9.29fc878"
homepage: "https://github.com/RhinoSecurityLabs/SleuthQL"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "RhinoSecurityLabs/SleuthQL"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# sleuthql

> Mines a Burp Suite proxy history export for SQL-injectable parameters and emits SQLmap-ready request files.

## What it's for

SleuthQL (Dwight Hohnstein, Rhino Security Labs) scans an exported Burp Suite proxy history for requests whose parameters or bodies contain SQL-like syntax, then rewrites each candidate parameter with SQLmap injection markers (`*`). Give it the XML export (Burp's "Save items" with base64-encoded requests) plus a comma-separated list of in-scope domains; it prints every matching parameter and path and writes one SQLmap-ready request file per finding. It understands URL parameters and `application/json`, `application/x-www-form-urlencoded`, and `multipart/form-data` bodies; it deliberately skips cookies and nested encodings (e.g. base64 inside JSON). Use it to triage a large proxy history into a small, high-yield batch workload for SQLmap.

## Typical use cases

### Triage a Burp export for one target domain

Find every SQL-looking parameter captured against a site and get SQLmap input files for each.

```
sleuthql -d example.com -f burpproxy.xml
```

Outcome: prints each request path and parameter containing SQL-like syntax, and creates a directory of request files with `*` injection markers — feed each one to `sqlmap -r <file>`.

### Scope several domains in one pass

Run the same triage across multiple hosts in a single Burp export.

```
sleuthql -d example.com,target.example.com -f burpproxy.xml
```

Outcome: same analysis and request-file output, restricted to requests whose host matches the comma-separated domain list; traffic to other hosts in the export is ignored.

*No corpus evidence — derived from upstream documentation.*
