---
name: "gh-dork"
categories:
  - blackarch-recon
version: "3.799f86f"
homepage: "https://github.com/molly/gh-dork"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "molly/gh-dork"
github-stars: 136
github-pushed: "2022-03-12"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gh-dork

> GitHub dorking from the command line: run a newline-separated dorks file against users, organizations or repos and collect every matching result.

## What it's for

gh-dork (molly) automates GitHub dorking — searching an account's or organization's repositories with expressions like `filename:.env` or `extension:pem` that surface carelessly committed files. The only required input is a dorks file (`-d`, one dork per line; techgaun's `github-dorks.txt` list is the usual seed). Scope it to a single user (`-u`), a users file (`-uf`), an organization (`-org`), an orgs file (`-of`) or one repo (`-r`); results print to the terminal and can additionally be written per-dork into an output directory. Authentication is via the `GH_TOKEN` environment variable (or `GH_USER`/`GH_PASS`; two-factor is prompted) — without it the script still runs but is throttled to GitHub's low unauthenticated search limits, and `GH_URL` points it at a GitHub Enterprise instance. It finds and collects matches; judging what a match exposes is your job.

## Typical use cases

### Run a dorks file against your own account

Audit what you are leaking.

```
gh-dork -d dorks.txt -u user
```

Outcome: searches every repository of the given user with each dork in `dorks.txt` and prints each matching repo/file as it is found.

### Sweep a whole organization

Check an org's repos in one pass.

```
gh-dork -d dorks.txt -org example-org
```

Outcome: applies every dork to the organization's repositories and prints matches; use `-of orgs.txt` instead to iterate over a list of organizations.

### Screen user/org lists for valid, searchable accounts

Filter dead accounts out of large lists.

```
gh-dork -d dorks.txt -of orgs.txt -vif valid_orgs.txt
```

Outcome: while running the first dork, writes every org that actually exists and has public code to `valid_orgs.txt` and skips the invalid ones for all later dorks — feed that file back as `-of` to make subsequent runs faster.

### Dork a single repository

Focus on one codebase.

```
gh-dork -d dorks.txt -r user/repo
```

Outcome: runs the full dorks file against just the named repository and prints matching files — a quick pre-audit of a fork or vendor repo.

### Save per-dork result files

Keep the evidence for triage.

```
gh-dork -d dorks.txt -u user -o results
```

Outcome: creates one file per dork under the `results/` directory and writes each dork's matches there as well as printing them — note the directory is cleared first, so point it at an empty or fresh path.

*No corpus evidence — derived from upstream documentation.*
