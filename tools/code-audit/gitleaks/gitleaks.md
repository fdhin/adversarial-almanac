---
name: "gitleaks"
categories:
  - code-audit
homepage: "https://github.com/gitleaks/gitleaks"
origin: "hacktricks"
recipe: "upstream"
packaged-by: [arch]
build-system: go
license: see repository
meta-checked: "2026-11-13"
github-repo: "gitleaks/gitleaks"
github-match: "exact"
github-stars: 30985
gh-checked: "2026-11-13"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# gitleaks

> Scan git repositories, directories, files or stdin for hardcoded secrets — passwords, API keys and tokens — past or present.

## What it's for

Gitleaks detects secrets in git history, plain files and directories, and
anything piped to it on stdin. It walks `git log -p` patches for the `git`
command, so committed-then-"removed" credentials are still found, and uses a
large rule set (regex + entropy) to flag API keys, tokens and passwords with
the rule ID, file, line, commit and a fingerprint per finding. Reach for it
before publishing a repo, during code audit, or to triage how far a leaked
credential spread through history. It does not verify whether a found secret
is still live, and it is not a generic source-code vulnerability scanner. The
upstream now declares the tool feature-complete (security fixes only). Note
the project moved from `zricethezav/gitleaks` to `gitleaks/gitleaks` — both
paths resolve to the same repository.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Scan a repository's full git history

Audit every commit of a local clone for secrets.

```
gitleaks git -v .
```

Outcome: prints each finding (secret, RuleID, file, line, commit, author,
fingerprint); verbose mode shows the offending line content. Exit code is 1
when leaks are found, 0 when clean — usable directly in CI gates.

### Scan directories or single files

Check a source tree or artifact that is not a git repo.

```
gitleaks dir -v /path/to/source
```

Outcome: same finding format as the git scan, applied to files on disk;
without a target argument it scans the current working directory.

### Scan data streamed on stdin

Screen a single file, a dump or command output without touching disk layout.

```
cat some_file | gitleaks -v stdin
```

Outcome: consumes the stream and reports any secrets matched by the rule
set; nothing is written to disk.

### Write a structured report

Produce a machine-readable report for triage or CI artifacts.

```
gitleaks git --report-format json --report-path gitleaks-report.json .
```

Outcome: writes `gitleaks-report.json` containing every finding (also
supports csv, junit, sarif and template formats via `--report-format` and
`--report-template`); the console still summarizes the total.

### Re-scan against a baseline

Ignore already-known findings and see only new ones on a noisy repo.

```
gitleaks git --report-path gitleaks-report.json .
gitleaks git --baseline-path gitleaks-report.json --report-path findings.json .
```

Outcome: the first run records current findings as the baseline; the second
run's `findings.json` contains only issues that were not in the baseline.

### Use a custom rule configuration

Extend or replace the built-in rules for project-specific secrets.

```
gitleaks git --config custom-gitleaks.toml .
```

Outcome: loads your TOML config (own rules, or rules extending the default
set via `use = ...`) instead of the built-in defaults; precedence is
`--config`, `GITLEAKS_CONFIG`, `GITLEAKS_CONFIG_TOML`, then a
`.gitleaks.toml` inside the target path.

### Scan only a commit range or branch set

Focus history review on a review window instead of the whole repo.

```
gitleaks git -v --log-opts="--all main..feature-branch" .
```

Outcome: passes the options through to the underlying `git log -p`, so only
the selected commits/branches are scanned; any valid `git log` option
(`--since`, `-n`, `--all`) works the same way.
