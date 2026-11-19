---
name: "gitmails"
categories:
  - blackarch-recon
version: "0.1.1.r6.g8aa8411"
homepage: "https://github.com/giovanifss/gitmails"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "giovanifss/gitmails"
github-stars: 151
github-pushed: "2019-05-05"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gitmails

> Collects the author name/email pairs hidden in git commit histories across GitHub, GitLab, and Bitbucket for a user, organization, or single repository.

## What it's for

Every git commit records an author name and email. Gitmails leverages that: given a target on GitHub, GitLab, or Bitbucket (all three by default), it lists the target's repositories, clones them (or walks commit history via the API), and analyzes every commit to extract the unique author identities behind the code. The result is a table of Name-Email pairs — useful for OSINT pivoting, mapping an organization's developers, and building phishing-era contact lists from public data. It is a 2019-era tool whose upstream is archived; the author points users to the maintained shell port, Gitmails-sh, for current use.

## Typical use cases

### Harvest emails for a single user

```
gitmails -u user
```

Outcome: clones the user's public repositories across GitHub, GitLab, and Bitbucket, analyzes commit history, prints the profile information followed by a table of unique Name-Email pairs. Add `--raw` for comma-separated output or `-f emails.csv` to store results in headerless CSV.

### Collect emails for a whole organization

```
gitmails -o target --include-users
```

Outcome: enumerates all repositories of organization `target`, analyzes their commit histories, and — with `--include-users` — also processes the organization's public members (GitHub only), giving org-wide developer identities. Note that GitLab usernames are case-sensitive.

### Query without cloning

```
gitmails --api -u user
```

Outcome: collects commit author information purely through the host APIs instead of cloning repositories — slower and unauthenticated requests get throttled, but nothing is written to disk. Per-host collection can be disabled with `--no-github`/`--no-gitlab`/`--no-bitbucket`.

*No corpus evidence — derived from upstream documentation.*
