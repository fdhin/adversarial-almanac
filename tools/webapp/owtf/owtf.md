---
name: "owtf"
categories:
  - blackarch-webapp
version: "v2.7.0.r0.g2c1c29d"
homepage: "https://www.owasp.org/index.php/OWASP_OWTF"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-06"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "owtf/owtf"
github-stars: 1970
github-pushed: "2026-11-03"
github-archived: false
github-issues: 102
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 145
gh-activity-checked: "2026-11-09"
---

# owtf

> OWASP Offensive Web Testing Framework: a plugin-driven testing manager that separates work by how much traffic it sends.

## What it's for

OWTF is an OWASP project focused on penetration-testing efficiency: it aligns tests to standards (OWASP Testing Guide v3/v4, OWASP Top 10, PTES, NIST) and runs them as plugins separated by traffic type — passive (nothing sent to the target), semi-passive (normal browsing traffic), and active (direct vulnerability probing). It is resilient (a crashing tool does not kill the run; partial output is kept), supports pausing and resuming, exposes a REST API, and presents results in a web interface with an interactive, rankable report. It is not a silver bullet: plugin output still needs human interpretation to demonstrate impact.

## Typical use cases

### Launch a testing session

```
owtf
```

Outcome: starts the framework and serves its web interface at `http://localhost:8009`, where targets are added, plugin groups (passive/semi-passive/active) launched, and the ranked report reviewed.

### Run directly from a source checkout

```
python -m owtf
```

Outcome: runs OWTF from an activated virtualenv in the repository directory (the developer workflow — code changes are reflected immediately); the web interface is served the same way.

No corpus evidence — derived from upstream documentation.
