---
name: "github-dorks"
categories:
  - blackarch-recon
version: "106.16dc173"
homepage: "https://github.com/techgaun/github-dorks"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "techgaun/github-dorks"
github-stars: 3305
github-pushed: "2026-09-15"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 18
gh-activity-checked: "2026-11-09"
---

# github-dorks

> A dork collection plus a Python CLI that runs those GitHub search queries against a repository, a user, an organization, or a local working tree.

## What it's for

GitHub Search can surface sensitive data — private keys, credentials, tokens, internal hostnames. github-dorks bundles a curated dictionary of such queries (organized into categories like `cloud`, `private-keys`, `saas`, `identity`, `devops`, `ai`) and ships a command-line tool, `github-dorks`, that executes them via the GitHub Search API against a target you specify: one repository, all repositories of a user or organization, a GitHub Enterprise instance, or an already-cloned local checkout. Use it to audit your own or a client's public footprint for accidentally committed secrets. Unauthenticated search is heavily rate-limited, so authenticate with `GH_TOKEN` (or `GH_USER`/`GH_PWD`). Local scans need no credentials or network at all.

## Typical use cases

### Dork a single repository

```
github-dorks -r target/target-repo
```

Outcome: runs every query in the bundled dork dictionary against that repository through the GitHub Search API and prints each matching query with the files it found, ending with a summary of queries, matches, failures, and elapsed time.

### Sweep all repositories of a user or organization

```
GH_TOKEN=<github_token> github-dorks -u target
```

Outcome: iterates every public repository of user or organization `target`, running the dork set against each; authenticating raises the API rate limit enough for a full sweep. The same form works against a GitHub Enterprise instance by setting `GH_URL=https://github.example.com`.

### Export structured results

```
github-dorks -u target --format jsonl -o results.jsonl
```

Outcome: writes newline-delimited JSON records (one per finding) to `results.jsonl` instead of terminal text; `--format csv|json` and `--force` to overwrite an existing file are also supported. Progress and summaries go to stderr so stdout stays machine-readable.

### Audit a local clone offline

```
github-dorks --local ./target-repo -c private-keys -c cloud
```

Outcome: scans the local working tree's files directly (no GitHub credentials or network needed), applying only the selected dork categories — a fast pre-push self-check for secrets.

*No corpus evidence — derived from upstream documentation.*
