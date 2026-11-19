---
name: "sandy"
categories:
  - blackarch-scanner
version: "6.531ab16"
homepage: "https://github.com/donctl/sandy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "donctl/sandy"
github-stars: 39
github-pushed: "2014-09-30"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sandy

> Python 2 framework for assessing encryption on Samsung phones, driven by per-project module runs.

## What it's for

sandy bundles modules that stage different attack scenarios against encrypted Samsung phones (workflows around Samsung's device encryption, as presented by the authors at Derbycon 3.0). Everything is project-scoped: each run takes a project name and the modules communicate by reading and writing `<project_name>_<filename>` artifacts under the results folder, so a chain of modules keeps working the same target. It was written for Python 2.7 with the pexpect, pbkdf2 and pyCrypto modules and targets 2013-era devices; its own disclaimer restricts it to explicitly consented testing.

## Typical use cases

### Start an assessment project

Create the shared workspace the modules chain through.

```
sandy -p assessment
```

Outcome: launches the framework for the project `assessment`; every file a module produces is named `assessment_<filename>` under the results folder, which is how the other modules find each other's output. Upstream form is `main.py [-h] -p project_name`; the packaged `sandy` wrapper runs it under python2.

*No corpus evidence — derived from upstream documentation (donctl/sandy README; packaged entry point from the BlackArch recipe).*
