---
name: "shhgit"
categories:
  - blackarch-recon
version: "67.bac0c7d"
homepage: "https://github.com/eth0izzle/shhgit"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-03-01"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "eth0izzle/shhgit"
github-stars: 3993
github-pushed: "2025-02-28"
github-archived: false
github-issues: 33
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shhgit

> Watch GitHub, Gists, GitLab and BitBucket public events in real time and flag committed secrets — or scan local directories.

## What it's for

shhgit consumes the public event streams of GitHub/Gist (a token is required), GitLab and BitBucket, downloads matching repositories, and tests files against around 150 signatures — API keys, private keys, credential files — plus entropy checks, printing hits as they happen; the Docker deployment adds a live web interface for a team. It can equally scan a local directory (CI-friendly) or ignore signatures entirely and search for a string you specify. Tokens, blacklists and the signature list all live in config.yaml. The project is no longer maintained, so its signatures age — treat matches as leads to verify, and keep the GitHub token minimal.

## Typical use cases

### Watch public repositories for secrets

```
shhgit
```

Outcome: with at least one GitHub token under github_access_tokens in config.yaml, streams every signature match from newly pushed public code in real time to the console.

### Scan a local directory

```
shhgit --local /path/to/repo --csv-path findings.csv
```

Outcome: recursively scans the given directory with the same signature set — no API tokens needed — and writes every match to the CSV, suitable for a CI gate or reviewing a donated codebase.

### Hunt one string instead of signatures

```
shhgit --search-query AWS_ACCESS_KEY_ID=AKIA
```

Outcome: skips signature matching and reports files containing the string/regex — useful when you know the exact format of the secret you are looking for.

*No corpus evidence — derived from upstream documentation.*
