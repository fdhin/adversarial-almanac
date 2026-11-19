---
name: "gitrecon"
categories:
  - blackarch-recon
version: "30.6467e78"
homepage: "https://github.com/GONZOsint/gitrecon"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "GONZOsint/gitrecon"
github-stars: 325
github-pushed: "2021-03-25"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gitrecon

> OSINT tool that reconstructs a GitHub or GitLab profile — including email addresses leaked via commits and public SSH keys — from just a username.

## What it's for

GitHub and GitLab link commits and activity to accounts via the account's email address, and that address is usually embedded in public commit data and events. gitrecon takes a username on either platform, pulls profile information (name, user ID, bio fields, creation dates), extracts the commit-leaked email addresses, and lists public SSH keys. From a bare handle you get the person behind it plus pivoting material (email, keys). Access tokens can be hardcoded into `modules/github_recon.py` / `modules/gitlab_recon.py` to avoid API bans on bulk runs. Caveat it states itself: commit data can be spoofed, so treat results as leads, not proof.

## Typical use cases

### Enumerate a GitHub user

```
gitrecon -s github user
```

Outcome: fetches the profile, commit-event emails, and public SSH keys for GitHub user `user`, printing them to the terminal; with `-o` the same data is also written under `results/<username>/`.

### Pull a GitLab profile with the avatar

```
gitrecon -s gitlab user -a -o
```

Outcome: same enumeration against Gitlab, `-a` downloads the account's avatar picture (useful for cross-platform image searches), and `-o` saves all output into `results/<username>/` for the case file.

*No corpus evidence — derived from upstream documentation.*
