---
name: "gitminer"
categories:
  - blackarch-recon
version: "8.071385f"
homepage: "https://github.com/UnkL4b/Gitminer3"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-11"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "UnkL4b/Gitminer3"
github-stars: 58
github-pushed: "2025-11-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# gitminer

> Modular GitHub dorking engine — runs GitHub search dorks, downloads the matching files, and regex-scans them for secrets, producing Markdown threat-intel reports.

## What it's for

GitMiner 3 (a complete refactor of the original UnkL4b GitMiner script) automates "advanced mining" of public GitHub repositories: you supply one dork or a file of dorks (GitHub search queries like `filename:.env DB_PASSWORD`), it executes them through the GitHub API, downloads matching files, and analyzes the content against a configurable YAML ruleset of regex patterns for API keys, tokens, passwords, and private keys. Findings are tracked in an SQLite history database and can be rendered into per-dork Markdown threat intelligence reports with severity analysis. It needs a GitHub personal access token (`--token` or `GITHUB_TOKEN`). It searches current code search results; it is not a commit-history auditor.

## Typical use cases

### Run a single dork with a report

```
gitminer -d "filename:.env DB_PASSWORD" --report
```

Outcome: searches GitHub for the dork (default 200 results), downloads matching files, scans them with the bundled pattern set, and writes a Markdown report under `reports/` with an executive summary and per-finding details; all operations are recorded in the SQLite history database.

### Batch a dork file at scale

```
gitminer -d dorks.txt -m 500 --report
```

Outcome: processes each dork in `dorks.txt` (one query per line, e.g. `filename:wp-config.php DB_PASSWORD`, `extension:pem private`), fetching up to 500 results per dork and generating a report per dork.

### Export the downloaded-file inventory

```
gitminer -d dorks.txt -o downloaded.csv --no-analyze
```

Outcome: `-o` writes a CSV summary of every file downloaded from GitHub; `--no-analyze` skips the secret-scanning step, so the run just bulk-collects the matching files under `raw/` for offline processing.

*No corpus evidence — derived from upstream documentation.*
