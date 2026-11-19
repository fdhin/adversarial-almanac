---
name: "githubcloner"
categories:
  - blackarch-misc
version: "36.20203e7"
homepage: "https://github.com/mazen160/GithubCloner"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "mazen160/GithubCloner"
github-stars: 429
github-pushed: "2025-05-14"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# githubcloner

> Bulk-clone every repository of given GitHub users or organizations into one output directory.

## What it's for

GithubCloner enumerates and clones all public repositories belonging to GitHub usernames or organizations, optionally including gists and organization members' personal repositories. It is used for OSINT source collection and for archiving an organization's code footprint before auditing it. The script queries the GitHub API for the repository list, then hands each URL to git; authentication and a custom API endpoint let it reach private repositories and self-hosted GitHub Enterprise instances. It only collects code — it does not scan or analyze what it clones.

## Typical use cases

### Clone all repositories of a user

Collect every repository (and optionally every gist) of one or more accounts into a local directory.

```
githubcloner --user user1,user2 -o /tmp/output --include-gists
```

Outcome: /tmp/output fills with a git clone of each repository and gist owned by the named accounts.

### Clone an organization and its members' repositories

Pull an organization's repositories plus the personal repositories of everyone in that organization.

```
githubcloner --org organization --include-org-members -o /tmp/output
```

Outcome: clones of all organization repositories and each member's own repositories land under /tmp/output.

### Enumerate repository URLs without cloning

Reconnaissance pass that prints the gathered repository URLs and exits, useful for counting a target's exposure before committing disk space.

```
githubcloner --user user --include-gists --echo-urls
```

Outcome: prints one repository/gist URL per line without writing any clones.

### Clone with authentication and private repositories

Use a user:token pair to raise API rate limits and include repositories the authenticated account can access.

```
githubcloner --org organization -o /tmp/output --authentication user:token --include-authenticated-repos
```

Outcome: clones the organization's repositories including private ones visible to the token's account.

### Clone from a GitHub Enterprise instance

Point the enumeration at a self-hosted GitHub Enterprise API endpoint instead of github.com.

```
githubcloner --org organization -o /tmp/output --api-prefix https://git.example.com/api/v3
```

Outcome: repositories are enumerated through the Enterprise API and cloned into /tmp/output.

*No corpus evidence — derived from upstream documentation.*
