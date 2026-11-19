---
name: "goofile"
categories:
  - recon
version: "1.5"
homepage: "https://github.com/sosukeinu/goofile"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "sosukeinu/goofile"
github-stars: 14
github-pushed: "2019-08-19"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# goofile

> Command-line filetype search — lists files of a given type hosted under a domain, via Google scraping or the Custom Search API.

## What it's for

goofile answers "what PDF/DOC/XLS/... files exist on this domain?" from the command line: you give it a domain and a filetype, and it returns matching URLs harvested from Google. It is the classic first step of a document hunt (metadata, exposed reports, directory listings) and of target-specific file fuzzing. This is the sosukeinu Python 3 continuation of the original Google Code `goofile` tool, which adds a `setup.py` (pip-installable `goofile` command) and an optional Custom Search Engine mode (`-k`/`-e`) that uses Google's official API key plus search-engine ID instead of raw scraping, avoiding bot detection at 100 free queries/day.

## Typical use cases

### Find files of a type on a domain

```
goofile -d example.com -f pdf
```

Outcome: queries Google for `site:example.com filetype-equivalent` matches and prints the URLs of discovered PDF files — the raw input for metadata extraction or manual review.

### Search via the Custom Search API

```
goofile -d example.com -f pdf -k <api-key> -e <search-engine-id> -q confidential
```

Outcome: uses the Google Custom Search JSON API instead of scraping (no CAPTCHAs on repeated runs), additionally narrowing results with the term `confidential` via `-q`; returns the matching file URLs on stdout.

*No corpus evidence — derived from upstream documentation.*
