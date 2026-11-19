---
name: "njsscan"
categories:
  - blackarch-code-audit
version: "0.3.1"
homepage: "https://pypi.org/project/njsscan/#files"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL2
meta-checked: "2026-11-09"
github-repo: "ajinabraham/njsscan"
github-stars: 458
github-pushed: "2026-10-27"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# njsscan

> Semantic-aware SAST for Node.js: a libsast pattern matcher plus semgrep rules flag insecure code patterns — with optional missing-security-controls checks — across JavaScript source trees.

*No corpus evidence — derived from upstream documentation.*

## What it's for

njsscan statically analyzes Node.js/JavaScript applications for insecure code patterns: XSS via untrusted input in responses, SQL injection from concatenated queries, open redirects, hardcoded secrets and similar. It combines two engines — the simple pattern matcher from libsast and the syntax-aware semgrep — and each finding carries a rule ID, OWASP and CWE mapping, severity and the matching lines. A `.njsscan` YAML file at the source root (or via `--config`) tunes file extensions, ignored paths, ignored rules and severity overrides, and a `// njsscan-ignore: rule_id` comment suppresses a false positive at line level. Output formats cover the usual CI ecosystems: JSON, SARIF, SonarQube, DefectDojo, GitLab SAST and HTML. It is also the engine behind the nodejsscan web UI. Python 3.10+, macOS and Linux only.

## Typical use cases

### Scan a Node.js project

```
njsscan /path/to/node/project
```

Outcome: both engines run over the source and print a formatted table per rule — rule ID, OWASP/CWE reference, severity, affected files with line numbers and the matched code — preceded by progress counts for Pattern Match and Semantic Grep.

### Save results as JSON

```
njsscan . --json -o results.json
```

Outcome: writes the same findings as JSON to `results.json` for scripted triage; `--sarif`, `--sonarqube`, `--defectdojo`, `--gitlab-sast` and `--html` select the other output formats.

### Upload findings to GitHub code scanning

```
njsscan . --sarif -o results.sarif
```

Outcome: produces a SARIF 2.1.0 report that GitHub's code-scanning upload action ingests, surfacing each finding as a repository security alert with file/line anchoring.

### Emit a GitLab SAST report

```
njsscan . --gitlab-sast -o gl-sast-report.json
```

Outcome: writes GitLab's native SAST report format, which a CI job declares under `artifacts: reports: sast` so findings appear in the vulnerability report and merge-request widget without any converter.

### Check for missing security controls

```
njsscan . --missing-controls
```

Outcome: additionally reports the absence of recommended security controls — WARNING-level hardening gaps rather than concrete vulnerabilities — using the same rule metadata and output formats.

### Drive the scan from a config file

```
njsscan . -c .njsscan
```

Outcome: applies the YAML configuration — `ignore-paths`, `ignore-filenames`, `ignore-rules`, `severity-filter`, `severity-overrides` and extension lists — instead of the defaults; a `.njsscan` file placed at the repository root is picked up automatically, and `-c` points at a differently named one.
