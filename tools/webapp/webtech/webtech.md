---
name: "webtech"
categories:
  - blackarch-webapp
version: "1.3.3"
homepage: "https://pypi.org/project/webtech/#files"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: GPL
meta-checked: "2026-11-09"
github-repo: "ShielderSec/webtech"
github-stars: 300
github-pushed: "2026-06-29"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# webtech

> Fingerprint the technologies behind a website from its headers, cookies, and content.

## What it's for

WebTech (maintained by Shielder) identifies the technologies a website uses — CMS, web server, frameworks, JavaScript libraries — by matching HTTP headers, cookies, and page content against a bundled database of signatures covering 1000+ technologies. Reach for it at the start of a web assessment to profile a target, or offline against a saved HTTP response file. It also works as an importable Python library and ships a Jython extension for Burp Suite. It only detects technologies; it does not test for vulnerabilities.

## Typical use cases

### Fingerprint a live target

```
webtech -u https://example.com/
```

Outcome: prints the target URL followed by a table of detected technologies (CMS, server, frameworks) drawn from the signature database.

### Fingerprint a saved response offline

```
webtech -u file://response.txt
```

Outcome: matches the headers, cookies, and body captured in the local response file against the database, without sending any traffic to the target.

### Scan a list of targets with JSON output

```
webtech --ul urls.txt --oj
```

Outcome: scans every URL in `urls.txt` and emits a machine-readable JSON report per target, suitable for piping into other tooling.

### Produce grepable one-line output

```
webtech -u https://example.com/ --og
```

Outcome: emits a grep-friendly single-line report per target for quick shell filtering.

*No corpus evidence — derived from upstream documentation (README and CLI help shipped in the packaged 1.3.3 release).*
