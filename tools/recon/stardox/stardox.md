---
name: "stardox"
categories:
  - blackarch-recon
version: "41.95b0a97"
homepage: "https://github.com/0xPrateek/Stardox"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0xPrateek/Stardox"
github-stars: 173
github-pushed: "2026-05-09"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# stardox

> Profiles the GitHub users who starred a repository — account stats and, where publicly exposed, their email addresses — into a tree view and CSV.

## What it's for

OSINT over a repository's stargazers: for each account it fetches total repositories, total stars, followers and following, renders the data as a list tree view, and extracts the stargazer's email address when one is publicly visible on their profile — profiling a project's audience through its stars. Results can be saved as CSV or the run limited to email harvesting only. It scrapes GitHub's web UI without documented authentication, so large repositories take a while and may hit rate limits. Python 3; installed by BlackArch as `stardox` (the source entry point is `stardox.py`).

## Typical use cases

### Profile a repository's stargazers

```
stardox -r https://github.com/user/repository
```

Outcome: fetches the stargazer list and prints each account's statistics in a tree view. Running `stardox` with no arguments prompts for the repository URL interactively; from a source checkout the invocation is `python3 stardox.py -r <url>`.

### Export the data to CSV

```
stardox -r https://github.com/user/repository -s stargazers.csv
```

Outcome: collects the same data and additionally writes it to `stargazers.csv`; without a path, `-s` saves to the desktop by default.

### Collect only the email addresses

```
stardox -r https://github.com/user/repository -e
```

Outcome: skips the full per-account dump and reports just the email addresses recoverable from stargazer profiles — the quickest path when only contact points are needed. Add `-v` for verbose progress output.

*No corpus evidence — derived from upstream documentation (0xPrateek/Stardox README) and the BlackArch packaging.*
