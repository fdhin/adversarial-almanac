---
name: "sh00t"
categories:
  - blackarch-misc
version: "214.8c7321f"
homepage: "https://github.com/pavanw3b/sh00t"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "pavanw3b/sh00t"
github-stars: 274
github-pushed: "2024-08-30"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sh00t

> Web-based task manager and test-case checklists for manual security assessments.

## What it's for

Sh00t (by Pavan of pavanw3b) is a Django web application that replaces generic notes tools for manual security testers. It models Projects → Assessments → Flags (test cases to work through) → Sh0ts (confirmed bugs): a new assessment is seeded from a bundled methodology — the OWASP Testing Guide and the Web Application Hacker's Handbook test cases, seeded by a reset script — and each confirmed finding is filed from a customizable bug-report template that pre-fills the technical description, affected URLs, steps to reproduce and fix recommendation, ready to export as Markdown (for example straight into a bug-bounty platform). Everything saves automatically as you work. It is a tracker and report generator, not a scanner: it never touches the target. Docker is the upstream-supported deployment; distro packages provide the `sh00t` command as a thin wrapper around Django's `manage.py`.

## Typical use cases

### First-run setup

```
sh00t migrate
sh00t createsuperuser
```

Outcome: creates the SQLite database schema and then interactively prompts for the administrator account used to log into the web UI — required once on a fresh non-Docker install before the first start.

### Start the web interface

```
sh00t runserver 0.0.0.0:8000
```

Outcome: serves the Sh00t web UI on port 8000; open http://127.0.0.1:8000 and log in with the account created during setup (Docker deployments default to `sh00t`/`sh00t`) to create a project, spin up an assessment, and work through its flags.

### Load the bundled methodologies

```
python /usr/share/sh00t/reset.py
```

Outcome: (re)seeds the OWASP Testing Guide and WAHH test cases as Modules and Flags so new assessments can pick from the full checklist; run it as the same user that owns the database files.

*No corpus evidence — derived from upstream documentation.*
