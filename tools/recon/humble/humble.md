---
name: "humble"
categories:
  - recon
version: "1.28-0parrot1"
homepage: "https://github.com/rfc-st/humble"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "rfc-st/humble"
github-stars: 383
github-pushed: "2026-11-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 549
gh-activity-checked: "2026-11-09"
---

# humble

> Graded, security-oriented analyzer of HTTP response headers with multi-format reporting.

## What it's for

humble (rfc-st) fetches a URL and grades its HTTP response headers against current security best practices, flagging deprecated or insecure headers, missing protective headers and protocol issues, with guidance on remediation. It fits both interactive auditing (console report) and automation — CI/CD grading, PDF/CSV/JSON/HTML exports, and analysis of headers already captured through a proxy or saved as a HAR file. It is not a vulnerability scanner: it inspects response headers only, with TLS checks delegated to an external testssl.sh when requested.

## Typical use cases

### Overall findings for one site

Run the brief report that summarizes security posture.

```
humble -u https://example.com -b
```

Outcome: prints overall findings for the URL; omitting `-b` prints the detailed per-header analysis instead.

### Export a report for documentation

Produce a shareable artifact of the analysis.

```
humble -u https://example.com -o pdf
```

Outcome: exports the detailed findings to a PDF report; `-o` also accepts `all, csv, html, json, md, txt, xlsx, xml`, with `-of` and `-op` setting the output filename and absolute path.

### Gate a deployment in CI/CD

Fail a pipeline when the grade is below the bar.

```
humble -u https://example.com -cicd A
```

Outcome: prints CI/CD-oriented analysis output and warns when the result does not reach grade `A` (grades run from `E` to `A+`), giving pipelines a machine-checkable quality gate.

### Analyze headers already captured

Grade traffic exactly as your proxy observed it, without a fresh request.

```
humble -if headers.txt
```

Outcome: analyzes the input file — a curl `--dump-header` capture or a HAR file — and prints the same findings while never contacting the site directly; combine with `-p http://127.0.0.1:8080` to route live analyses through a proxy.

*No corpus evidence — derived from upstream documentation.*
