---
name: "glue"
categories:
  - blackarch-automation
version: "380.8703380"
homepage: "https://github.com/OWASP/glue"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: APACHE
meta-checked: "2026-11-09"
github-repo: "OWASP/glue"
github-stars: 529
github-pushed: "2023-09-05"
github-archived: false
github-issues: 73
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# glue

> OWASP framework that chains security tools into one analysis pipeline and normalizes their findings.

## What it's for

OWASP Glue (Ruby) is a backbone for automating a security-analysis pipeline: it runs a series of tool "tasks" against a target — a filesystem path, a git repository, or a live system — and merges their findings into a common report format (text, JSON, CSV, Jira, Slack). Tasks are grouped into stages (file, code, live) covering e.g. clamav and hashdeep, brakeman/bundle-audit/eslint/findsecbugs, and OWASP ZAP. Glue orchestrates and reports; it finds nothing itself, and it is intended to be extended by copying an existing task and adapting it. Suited to DevSecOps CI usage rather than one-off pentest work.

## Typical use cases

### Run a whole stage against a codebase

```
glue -l code /path/to/repo
```

Outcome: every code-stage task (brakeman, bundle-audit, eslint, findsecbugs, ...) that is available runs against the target and findings are printed in the default text format; `-l file` or `-l live` select the filesystem and live-system stages instead.

### Run or exclude a single task

```
glue -t brakeman /path/to/repo
glue -l code -x brakeman /path/to/repo
```

Outcome: the first invocation runs only brakeman; the second runs all code tasks except brakeman — useful when one tool is missing or too slow in your environment.

### Emit machine-readable findings for CI

```
glue -l code -f json /path/to/repo
```

Outcome: findings are serialized to JSON on stdout instead of text (csv and jira are also supported), ready to be archived or gate a pipeline step.

### Freeze a complex invocation into a config file

```
glue -l code -f json /path/to/repo --create-config
```

Outcome: prints the equivalent YAML configuration; saved as `./config/glue.yml`, `~/.glue/config.yml`, or `/etc/glue/config.yml` it is merged into future runs, keeping credentials and long option sets out of the command line.

*No corpus evidence — derived from upstream documentation.*
