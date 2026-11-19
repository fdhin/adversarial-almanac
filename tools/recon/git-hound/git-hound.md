---
name: "git-hound"
categories:
  - blackarch-recon
version: "258.15701b7"
homepage: "https://github.com/tillson/git-hound"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-11-23"
build-system: go
license: MIT
aliases: ["githound"]
meta-checked: "2026-11-09"
github-repo: "tillson/git-hound"
github-stars: 1472
github-pushed: "2026-02-10"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# git-hound

> Pattern-matching, patch-attacking, batch-catching secret snatcher: feed it a keyword or GitHub dork and it scans all matching public repos and Gists — files and commit history — for exposed API keys.

## What it's for

GitHound (tillson, Go) finds leaked secrets across GitHub as a whole rather than in repos you already know. Input is a keyword or dork (e.g. a company domain or `AKIA`) via stdin or `--query`; GitHound drives GitHub's Code Search API over every matching repository and Gist, then goes deeper than search previews: `--dig-files` greps full file contents, `--dig-commits` walks commit history for deleted or reverted credentials, and a scoring layer (exact service regexes from the Gitleaks rules, plus entropy, dictionary and uniqueness checks, with base64 decoding) filters the noise down to high-certainty API keys. It needs a GitHub token in `config.yml` (or `$HOME/.githound/`) or the `GITHOUND_GITHUB_TOKEN` environment variable, and `--rules` accepts custom regex sets. It surfaces candidates; verifying a key still works is a separate step.

## Typical use cases

### Snatch AWS keys by prefix

One-liner from stdin.

```
echo "AKIA" | git-hound
```

Outcome: searches all public GitHub code for the AWS key prefix, digs into matching files for secret-shaped strings, and prints scored hits; `git-hound --query "AKIA"` is the equivalent without the pipe.

### Deep dig on an organization's domain

Hunt employee-leaked tokens for a bug bounty target.

```
echo "example.com" | git-hound --dig-files --dig-commits --many-results --threads 100
```

Outcome: searches GitHub for the domain string, then digs full file contents and commit history of matches using 100 threads; `--many-results` works around result limits to scrape more than 100 pages, and `--languages common-languages.txt` can prefilter by language.

### Pipe custom key regexes into your own validator

Chain GitHound into a testing pipeline.

```
echo "example.com" | git-hound --dig-files --dig-commits --rules custom-regexes.txt --results-only
```

Outcome: applies your own regex list (`--rules` also accepts a GitLeaks rules folder) to everything dug up, and `--results-only` prints just the matched strings so a downstream script can test each key against the service.

### Emit JSON for tooling

Machine-readable output for dashboards and triage.

```
git-hound --query "api_key" --json
```

Outcome: runs the query and prints results as JSON records instead of the human table — suitable for logging into a tracker or the GitHound Explore dashboard (`--dashboard`).

*No corpus evidence — derived from upstream documentation.*
