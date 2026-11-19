---
name: "yasca"
categories:
  - blackarch-code-audit
version: "2.1"
homepage: "http://www.scovetta.com/yasca.html"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "scovetta/yasca"
github-stars: 182
github-pushed: "2022-01-27"
github-archived: true
github-issues: 22
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# yasca

> "Yet Another Source Code Analyzer" — a 2009-era multi-language static-analysis engine (PHP core) whose plugin rules scan many languages, optionally calling external tools like PMD or FindBugs and rendering grouped reports.

*No corpus evidence — derived from upstream documentation.*

## What it's for

One-command static analysis across a mixed-language source tree: Yasca recursively scans a directory with its default plugin rules — cross-site scripting, SQL injection, unsafe file handling, resource leaks, weak patterns in Java/JSP/PHP/ASP/JavaScript/C/C++ and more — and renders the findings as a grouped report. Its second trick is orchestration: wrapper plugins invoke external analyzers (PMD, FindBugs, CppCheck, RATS, PHPLint, ClamAV, ...) installed in a separate tools directory and merge their results into the same report. Treat it as a legacy instrument, not a current recommendation: upstream is explicitly deprecated in favor of DevSkim, the engine targets the PHP 5 era (the packaged launcher pins a bundled `php.ini`), and Java is needed for some integrations. Its value today is cheap broad coverage on old codebases and comparing against historical baselines; for active development, use a maintained scanner.

## Typical use cases

### Scan a source tree with the default rules

```
yasca /srv/legacy-app -o /tmp/yasca-report.html
```

Outcome: matching files are scanned with the built-in plugin set (default ignored extensions: exe, zip, jpg, gif, png, pdf, class), progress logs to the console, and the default grouped HTML report is written to the `-o` path with findings grouped by category and file.

### Produce a machine-readable report instead

```
yasca /srv/legacy-app -r CSVReport -o /tmp/findings.csv
```

Outcome: the same scan rendered as CSV, one row per finding. `-r` selects any built-in report class — `XMLReport`, `ConsoleReport`, `HTMLReport`, `DetailedReport`, `SQLReport`, `HTMLGroupReport` (default) — and the run ends with "Results have been written to" naming the output file.

### Cut the report down to high-severity findings

```
yasca /srv/legacy-app -l 1 -o /tmp/report.html
```

Outcome: only findings rated at severity 1 — the strictest slice — make it into the report; the default `-l 5` includes every severity, so raising the number adds detail rather than filtering.

### Fold external analyzers into the scan

```
yasca -sa /opt/static-tools /srv/legacy-app
```

Outcome: Yasca discovers third-party analyzers installed under `/opt/static-tools` (point `-sa`/`--sa_home` at the directory, or set `SA_HOME`) and runs the matching wrapper plugins alongside the built-in rules, merging their findings into one report; some integrations additionally require a Java runtime.

### Skip noisy files and extensions

```
yasca /srv/legacy-app -i "exe,zip,min.js,map" -o /tmp/report.html
```

Outcome: the default ignored-extension list is replaced by the given one, so minified JavaScript and source maps are excluded from scanning along with binaries — trimming scan time and report noise on vendored trees.
