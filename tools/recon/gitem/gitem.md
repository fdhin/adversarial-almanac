---
name: "gitem"
categories:
  - blackarch-recon
version: "104.d40a1c9"
homepage: "https://github.com/mschwager/gitem"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mschwager/gitem"
github-stars: 219
github-pushed: "2023-05-22"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gitem

> Python tool for GitHub organizational reconnaissance — profiles an organization, its repositories, and its members, including contributor emails.

## What it's for

gitem collects information about a GitHub organization at several levels of granularity using only public GitHub API data: the organization profile and public members, per-repository details with contributor lists, and per-user profiles with their organizations, repositories, and email addresses. It is aimed at OSINT, spearphishing preparation, recruitment research, competitive analysis, and OPSEC self-assessment of what an organization leaks publicly. It is purely read-only reconnaissance; it does not scan code for secrets. Output is truncated by default — pass `--verbose` for full listings.

## Typical use cases

### Profile an organization

Get the organization's profile, public members, and repositories with stars, forks, and push dates.

```
gitem organization target
```

Outcome: prints the org's website, description, location, email and creation date, followed by its public members and every public repository with watchers, stars, forks, and last-push timestamps — a one-shot inventory for phishing-target or OPSEC review.

### Inspect one repository and its contributors

Drill from the organization listing into a single repository.

```
gitem repository target target-repo
```

Outcome: prints the repository's metadata (language, homepage, stars, forks) and its top contributors ranked by commit count — useful for finding the people who actually write the code, not just the org page.

### Enumerate a user across GitHub

Analyze a specific account, in parallel across its repositories.

```
gitem --processes 4 user user
```

Outcome: fetches the user's profile, organization memberships, repositories, and the email addresses visible in their activity; `--processes 4` parallelizes repository analysis. Combine contributor lists from the repository view with this to pivot from an org to the people behind it.

*No corpus evidence — derived from upstream documentation.*
