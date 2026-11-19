---
name: "xssscan"
categories:
  - blackarch-webapp
version: "17.7f1ea90"
homepage: "https://github.com/gwroblew/detectXSSlib"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: other
license: MS-PL
meta-checked: "2026-11-09"
github-repo: "gwroblew/detectXSSlib"
github-stars: 31
github-pushed: "2013-06-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xssscan

> Command-line detector of XSS attack patterns in URLs, built on an optimized subset of OWASP CRS ModSecurity rules.

## What it's for

xssscan is the CLI tool bundled with detectXSSlib (by Grzegorz Wroblewski), a C library that detects XSS attack patterns in URLs using a performance-optimized subset of the OWASP Core Rule Set's ModSecurity rules. URLs are URL-decoded, cleaned of noise, tokenized and matched against the rule set, which makes the tool suited to triaging large URL sets — log mining and traffic analysis — rather than attacking anything: it classifies URLs as carrying (or not carrying) XSS patterns. The same library also ships an nginx module; the CLI is compiled from `tools/xssscan` with g++.

## Typical use cases

### Classify a single URL

```
xssscan -r "http://example.com/?q=<script>alert(1)</script>"
```

Outcome: prints the URL flagged as an XSS attempt, with `-r` also showing the specific rule number that matched (useful for debugging or statistics).

### Triage a large URL file

```
xssscan -a -d -f urls.txt
```

Outcome: processes every URL in `urls.txt`, replaces host names with `www.example.com` in the output (`-a`) and deduplicates by host (`-d`), yielding a sanitized detection report suited for sharing or counting.

### List only clean URLs

```
xssscan -x -f urls.txt
```

Outcome: inverts the filter and prints only the URLs where no XSS pattern was detected — handy to find what slipped past the rule set.

*No corpus evidence — derived from upstream documentation (tools/xssscan/ReadMe.txt of gwroblew/detectXSSlib).*
