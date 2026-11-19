---
name: "jira-lens"
categories:
  - recon
homepage: "https://github.com/MayankPandey01/Jira-Lens"
origin: "howtohunt"
recipe: "upstream"
packaged-by: []
build-system: python
license: MIT
meta-checked: "2026-11-13"
github-repo: "MayankPandey01/Jira-Lens"
github-match: "exact"
github-stars: 345
github-pushed: "2024-12-31"
github-issues: 1
gh-checked: "2026-11-13"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# jira-lens

> Fast vulnerability scanner for Atlassian Jira instances: 25+ checks covering known CVEs and information disclosures, against one URL or a whole list.

## What it's for

Jira-Lens is a Python 3 vulnerability scanner specialized in Atlassian Jira.
Given a Jira instance URL it runs 25+ checks — known CVEs plus multiple
information-disclosure probes (user enumeration, dashboards, filters,
gadgets and similar Jira-specific exposures) — and writes a per-target
report with the findings. Reach for it during external recon when a target
runs Jira (Server or Data Center variants) and you want a one-shot pass over
the classic misconfigurations instead of hand-testing each path. It only
tests for known issues; it is not an authenticated Jira audit or a general
web scanner.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Scan a single Jira instance

Run the full check set against one URL.

```
python3 Jira-Lens.py -u https://jira.example.com
```

Outcome: all 25+ checks run against the instance; a findings report is
written to the output folder. Add `-i` when the instance uses a self-signed
certificate (disables SSL verification), and `-o /path/to/dir` to redirect
the reports.

### Scan a list of Jira instances

Cover a whole external-asset sweep in one run.

```
python3 Jira-Lens.py -f targets.txt
```

Outcome: reads one properly-formed URL per line and scans each in turn,
producing a separate report per instance in the output folder — the natural
companion to subdomain enumeration filtered to Jira hosts.

### Run it from Docker

Keep the tool and its dependencies out of the host Python.

```
docker run --rm -v "$(pwd)/output":/app/Jira-Lens/output jira-lens https://jira.example.com
```

Outcome: builds from the repo's Dockerfile and scans the given URL with
reports volume-mounted back to `./output` — no local `setup.py install`
needed.
