---
name: "token-hunter"
categories:
  - blackarch-social
version: "343.3358a33"
homepage: "https://github.com/GitLab-Red-Team/token-hunter"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-05"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "GitLab-Red-Team/token-hunter"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# token-hunter

> Scans a GitLab group's projects and members for exposed secrets hidden in snippets, issues, merge requests and CI job logs.

## What it's for

Token-Hunter (GitLab Red Team) answers "what has this GitLab organization leaked into places git-history scanners never look?". Given a group ID or project ID it enumerates the group's projects — optionally including the personal projects of every group member — and sweeps GitLab-specific assets (snippets, issues and their discussions, merge requests and their discussions, public CI job logs) with TruffleHog-style regular expressions plus patterns for GitLab-specific tokens, complementing commit-history tools such as GLeaks/gitrob. It works against gitlab.com and self-hosted instances, and every run requires a GitLab Personal Access Token with the `api` scope exported as `GITLAB_API_TOKEN`. The original GitHub repository has been removed; the project lives on at `https://gitlab.com/gitlab-com/gl-security/gl-redteam/token-hunter`, and the packaged snapshot is the last GitHub-side state.

## Typical use cases

### Enumerate a group's projects

```
export GITLAB_API_TOKEN=<access-token>
token-hunter -g my-group
```

Outcome: prints the URLs of every project under the group (find the group ID under the group name in the GitLab UI); with only `-g` no secret search is performed — this is the reconnaissance step before deciding what to sweep.

### Full secret sweep across all GitLab asset types

```
token-hunter -g my-group -msirj
```

Outcome: after listing group projects and each member's personal projects (`-m`), scans snippets (`-s`), issues and discussions (`-i`), merge requests and discussions (`-r`), and CI job logs (`-j`) for token patterns; matched secrets are printed as they are found. Warning inherited from upstream: this combination can pull a lot of data.

### Scope a sweep to one project

```
token-hunter -p my-project -sir
```

Outcome: same snippet/issue/merge-request search as the group sweep but limited to a single project ID or HTML-encoded name — useful when a group scan flagged one repository.

### Scan a self-hosted GitLab instance and keep the output

```
token-hunter -g my-group -ms -u https://gitlab.example.com -l findings.txt
```

Outcome: runs the group+members snippet sweep against a self-hosted instance instead of gitlab.com and appends all output to `findings.txt`; add `-x http://198.51.100.10:8080` to route the traffic through a proxy such as Burp/ZAP, and `-t` to strip timestamps and source IP from the log.

*No corpus evidence — derived from upstream documentation.*
