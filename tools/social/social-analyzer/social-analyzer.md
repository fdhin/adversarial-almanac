---
name: "social-analyzer"
categories:
  - blackarch-social
version: "0.45"
homepage: "https://pypi.org/project/social-analyzer/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "qeeqbox/social-analyzer"
github-stars: 24492
github-pushed: "2026-01-12"
github-archived: false
github-issues: 28
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# social-analyzer

> QeeqBox's profile-finder: searches a username (or name/email) across hundreds of social sites, scores each candidate profile and returns structured results.

## What it's for

Social Analyzer detects and analyses a person's presence across social-media platforms: given a username it runs fast, slow or special analysis modes over its site database, scores detected profiles (good/maybe/bad confidence) and can also extract profile metadata and text patterns. Unlike plain username-checkers it is designed to feed its output into other tooling — JSON output with per-site detection rates — and its site list is filterable by country or site type. It is a local analysis tool (no access control — never expose it as a service) and its detections are statistical, so human-review the "maybe" tier before acting on it.

## Typical use cases

### Fast username lookup across the whole site database

```
python3 -m social-analyzer --username "user" --mode fast
```

Outcome: probes the site database in fast mode and prints detected profiles with links and detection scores; a JSON or pretty report is written under the logs directory for later use.

### Target specific sites with machine-readable output

```
python3 -m social-analyzer --username "user" --websites "github" "reddit" --output json --method find --filter good
```

Outcome: only the named sites are checked; output is JSON (for piping into other tools) filtered to confidently-detected (`good`) profiles, skipping unknown/failed lookups.

### Discover the searchable site database

```
python3 -m social-analyzer --list
```

Outcome: prints every website social-analyzer can query with its detection metadata — use it to find valid names for `--websites`, or to scope a sweep with `--countries` or `--type` (for example `--type adult`) instead of typing site names.

*No corpus evidence — derived from upstream documentation.*
