---
name: "titus"
categories:
  - blackarch-scanner
version: "v1.2.11.r5.g4059973"
homepage: "https://github.com/praetorian-inc/titus"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: go
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "praetorian-inc/titus"
github-stars: 765
github-pushed: "2026-11-05"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 429
gh-activity-checked: "2026-11-09"
---

# titus

> High-performance secrets scanner based on NoseyParker.

## What it's for

Titus is Praetorian's secrets scanner: it sweeps files, directories, git history, public or private GitHub/GitLab repositories and Docker/OCI images for leaked credentials. Findings land in a local datastore (`titus.ds` by default) that you re-read with `titus report` (text, JSON or SARIF), explore interactively with `titus explore`, or filter through `titus rules`. Detected secrets can be validated against their source APIs to separate live credentials from placeholders, and `titus serve` streams findings to a bundled Burp Suite extension. It is a secrets-detection pipeline, not a general-purpose SAST tool.

## Typical use cases

### Scan a local file tree for secrets

```bash
titus scan path/to/code
```

Outcome: findings print to the console and persist in the `titus.ds` datastore; re-read them later with `titus report`, which also accepts `--datastore` to point at another scan's results.

### Scan a public GitHub repository

```bash
titus scan github.com/org/repo
```

Outcome: the repository is fetched and scanned with no API token required; for wider sweeps `titus github --org org` scans every repo in an org (`--token $GITHUB_TOKEN` adds private repos and higher rate limits) and `--git` walks git history to catch deleted secrets.

### Scan a Docker or OCI image

```bash
titus scan --docker alpine:latest
```

Outcome: the image is pulled directly from the registry (no docker daemon required) and its filesystem layers are scanned for credentials; tarballs from `docker save` and OCI layout directories work too.

### Validate detected secrets against source APIs

```bash
titus scan path/to/code --validate
```

Outcome: each detected secret is checked against its provider and marked confirmed, denied or unknown (4 concurrent validators by default, tunable with `--validate-workers`).

### Produce CI-ready reports

```bash
titus report --format sarif
```

Outcome: SARIF output suitable for GitHub Advanced Security code scanning; `--format json` emits machine-readable findings and `--repo`/`--namespace`/`--path` selectors narrow the report to specific sources.

*No corpus evidence — derived from upstream documentation (praetorian-inc/titus README Quick Start, Scanning Options and Viewing Scan Results sections).*
