---
name: "corsy"
categories:
  - blackarch-webapp
version: "69.2985ae2"
homepage: "https://github.com/s0md3v/Corsy"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "s0md3v/Corsy"
github-stars: 1543
github-pushed: "2022-09-17"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# corsy

> Lightweight CORS misconfiguration scanner with eleven bypass tests per target.

## What it's for

Corsy scans CORS implementations for the known misconfiguration classes: pre-domain and post-domain bypasses, backtick and unescaped-dot tricks, null-origin reflection, underscore abuse, wildcard and invalid values, plain origin reflection, plus third-party-origin and HTTP-scheme allowance. It is a single-file Python tool with one dependency (requests), takes a URL, a URL file or stdin, and for each finding prints a description, severity and exploitation note (suppressible with `-q`). It only identifies the weak policy; it does not perform the cross-origin read itself.

## Typical use cases

### Scan a single URL

One-target check with full findings.

```
corsy -u https://target.example.com
```

Outcome: runs all eleven tests against the endpoint and prints each misconfiguration found with description, severity and an exploitation hint — the hint tells you which origin shape would be accepted.

### Scan many URLs and export JSON

Bulk scan from a list with concurrency and a report.

```
corsy -i urls.txt -t 20 -d 2 -o cors-report.json
```

Outcome: scans every URL in `urls.txt` with 20 threads and a 2-second delay between requests, printing findings as they come and writing the complete results to `cors-report.json`.

### Scan from stdin in a pipeline

Chain it with another tool's output.

```
cat urls.txt | corsy
```

Outcome: reads URLs from stdin line by line and scans each — drops straight into a `subfinder`/`waybackurls` pipeline without a temp file.

### Send custom headers

Reach endpoints that expect a session or specific client.

```
corsy -u https://target.example.com --headers "User-Agent: GoogleBot\nCookie: SESSION=<value>"
```

Outcome: attaches the given headers to every probe request, letting the scan test authenticated responses.

No corpus evidence — derived from upstream documentation.
