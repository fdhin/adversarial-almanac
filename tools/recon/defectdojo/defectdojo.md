---
name: "defectdojo"
categories:
  - identify
version: "2.37.3-0kali7"
homepage: "https://github.com/DefectDojo/django-DefectDojo"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: make
meta-checked: "2026-11-09"
github-repo: "DefectDojo/django-DefectDojo"
github-stars: 5197
github-pushed: "2026-11-07"
github-archived: false
github-issues: 276
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2912
gh-activity-checked: "2026-11-09"
---

# defectdojo

> OWASP flagship vulnerability-management platform: import scanner findings, deduplicate, track and push them to Jira and Slack.

## What it's for

DefectDojo is a web application (Django) and REST API — an OWASP flagship
project — that sits downstream of your scanners. You register products and
engagements, import results from a large catalog of supported scanner output
formats, and the platform normalizes, deduplicates and enriches the findings
with heuristics that improve as you use it, then drives remediation workflow
and reporting, with integrations to push findings to systems like JIRA and
Slack. It is not a scanner itself and has no meaningful standalone CLI: day-
to-day interaction happens in the browser or through its documented REST API
v2 (see docs.defectdojo.com). Deployment options (Docker Compose is the
upstream default) are covered in install.md.

## Typical use cases

### Start the stack and recover the generated admin password

First-run bootstrap: the initializer creates a random admin credential that
you must fish out of the logs.

```
docker compose up -d && docker compose logs initializer | grep "Admin password:"
```

Outcome: brings up all DefectDojo containers in the background, then prints
the admin password generated on first initialization (the initializer can
take up to three minutes). Log in at the exposed HTTP endpoint with username
`admin` and that password, then create a product and import a first scan
report — sample reports suitable for a dry run live in the upstream
repository under `unittests/scans/`.

No corpus evidence — derived from upstream documentation.
