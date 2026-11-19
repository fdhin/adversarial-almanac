---
name: "brakeman"
categories:
  - blackarch-code-audit
version: "v8.1.0.r2.g4bfdad0c3"
homepage: "https://brakemanscanner.org/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-24"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "presidentbeef/brakeman"
github-stars: 7292
github-pushed: "2026-10-22"
github-archived: false
github-issues: 120
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 125
gh-activity-checked: "2026-11-09"
---

# brakeman

> Static-analysis security scanner for Ruby on Rails applications.

## What it's for

Brakeman scans the source of a Rails application for security vulnerabilities — SQL injection, cross-site scripting, mass assignment, unsafe redirects, command injection, and many more check types — without executing or even booting the application. It parses Rails conventions natively, so a default scan needs no configuration, and it reports each warning with a check name, confidence level (High/Medium/Weak), and file/line context. Reach for it as the fast first pass in a Rails security review or as a CI gate; it is Rails-specific, so plain Ruby or other frameworks are out of scope. Requires Ruby >= 3.2 to run and understands Rails 2.3 through 8.x code.

## Typical use cases

### Scan the app in the current directory

```
brakeman
```

Outcome: full scan of the Rails app rooted at the working directory; warnings print grouped by check with confidence levels, and the process exits non-zero when any security warning is found — the usual CI signal.

### Scan an app from anywhere

```
brakeman path/to/rails/app
```

Outcome: the same scan without cd-ing into the application root; paths in the report stay relative to the given root.

### Produce HTML and JSON reports at once

```
brakeman -o report.html -o report.json
```

Outcome: writes both an HTML report (with source excerpts around each warning) and a JSON report; the format follows each output file's extension, and multiple `-o` flags may be combined.

### Show only high-confidence findings

```
brakeman -w3
```

Outcome: output is filtered to High-confidence warnings only (`-w1` = all, `-w2` = Medium and above, `-w3` = High) — a quick way to cut noise during triage.

### Run or skip specific checks

```
brakeman -t SQL,ValidationRegex
brakeman -x DefaultRoutes,Redirect
```

Outcome: the first command runs only the named checks, the second runs everything except the listed ones. Check names are case-sensitive; the full list is in `brakeman --help`.

### Diff against a previous scan

```
brakeman --compare report-old.json
```

Outcome: runs a fresh scan and emits JSON containing two lists — fixed warnings and newly introduced warnings — for tracking regressions between commits or releases.

*No corpus evidence — derived from upstream documentation.*
