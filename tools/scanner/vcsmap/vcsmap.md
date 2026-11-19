---
name: "vcsmap"
categories:
  - blackarch-scanner
version: "v2.0.2.r1.g3889964"
homepage: "https://github.com/melvinsh/vcsmap"
packaged-by: [blackarch]
blackarch-builddate: "2025-10-02"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "melvinsh/vcsmap"
github-stars: 137
github-pushed: "2021-08-31"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vcsmap

> Plugin-based scanner that mines public GitHub code search for leaked secrets — AWS keys, wp-config.php databases, OAuth tokens — and writes the hits to CSV.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Hunting credentials accidentally committed to public repositories. Each vcsmap plugin pairs a GitHub code-search query with extraction logic for one secret type — the bundled set covers AWS access keys (config and credentials files), WordPress wp-config.php database credentials, Google OAuth client_secrets.json, Filezilla XML FTP logins, Solr dataConfig.xml JDBC credentials, Sublime Text GitHub tokens, Facebook app secrets and Instagram tokens. A run searches a number of result pages, extracts matching credentials, renders them as an ASCII table on the console and writes CSV files into the `output` directory. Authentication is by a GitHub `user_session` cookie exported into the `GITHUB_COOKIE` environment variable; without it, GitHub's code search is too locked down to sweep. A Ruby gem (upstream archived since 2021) that searches GitHub only — its planned GitLab support never shipped.

## Typical use cases

### See what the bundled plugins hunt

```
vcsmap list
```

Outcome: prints every plugin name with its one-line target description (for example `aws` — extracts AWS credentials from config and credentials files), which is the name you then pass to `run`.

### Sweep a few pages of search results for one secret type

```
GITHUB_COOKIE=<user_session value> vcsmap run aws 5
```

Outcome: authenticates with the cookie, pulls five pages of code-search matches (10 results per page), extracts the matching credentials, renders them as an ASCII table and appends CSV rows under the output directory. Add `--no-ascii` to skip the table when logging to a file.
