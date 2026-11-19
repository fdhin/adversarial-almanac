---
name: "dawnscanner"
categories:
  - blackarch-webapp
version: "v2.2.0.r15.g0d647fc"
homepage: "https://github.com/thesp0nge/dawnscanner"
packaged-by: [blackarch]
blackarch-builddate: "2025-10-02"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "thesp0nge/dawnscanner"
github-stars: 746
github-pushed: "2024-03-02"
github-archived: false
github-issues: 24
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dawnscanner

> Static analysis security scanner for Ruby web applications written with Rails, Sinatra or Padrino.

## What it's for

Dawnscanner reviews Ruby web application source for security issues. It parses the project's Gemfile.lock, detects the framework and Ruby version, and applies a knowledge base of 680+ checks — CVE bulletins synced from the NIST vulnerability database plus code-level checks (XSS and SQL injection sinks in views, OWASP Ruby on Rails cheatsheet rules). The knowledge base is a separate download unpacked into ~/dawnscanner/kb. The upstream command is `dawn`; BlackArch wraps it as `dawnscanner`. It is a static reviewer for code you have — not a live web scanner.

## Typical use cases

### Scan a project

```
dawnscanner scan /path/to/rails/app
```

Outcome: detects the framework, checks every gem in Gemfile.lock against the knowledge base, applies the code-level checks, and writes a report of vulnerabilities with mitigation hints under ~/dawnscanner/results/app/<scan-timestamp>/.

### Look up the issues affecting a gem

```
dawnscanner kb list rails
```

Outcome: prints every security issue in the knowledge base for the named gem (optionally scoped to a version, e.g. `kb list rails 6.0.0`) — a quick CVE lookup without scanning an application.

_No corpus evidence — derived from upstream documentation (README subcommand reference)._
