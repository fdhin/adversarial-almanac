---
name: "dradis-ce"
categories:
  - blackarch-recon
version: "v5.0.0.r829.g6ff9bf4"
homepage: "https://dradis.com/ce/"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: GPL
aliases: ["dradis"]
meta-checked: "2026-11-09"
github-repo: "dradis/dradis-ce"
github-stars: 845
github-pushed: "2026-11-07"
github-archived: false
github-issues: 38
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1829
gh-activity-checked: "2026-11-09"
---

# dradis-ce

> Dradis Community Edition: a self-hosted web app where a pentest team collects findings, imports tool output and assembles the final client report.

## What it's for

Dradis CE is the open-source edition (GPLv2) of the Dradis collaboration and reporting framework, maintained since 2007. During an engagement it gives every tester one shared workspace: a tree of hosts and nodes with notes and evidence attached, uploaders that import results from Burp Suite, Nessus, Nmap, Nikto, OpenVAS, Qualys, ZAP and more, and a report builder that turns the collected evidence into a consistent deliverable so the team stops hand-merging screenshots into a document. It is a server application you run yourself; a commercial Pro edition adds team workflow and report automation on top. It is not a scanner — it organizes and reports on results produced by other tools.

## Typical use cases

### Start the collaboration server

```
dradis-ce
```

Outcome: the packaged launcher starts the bundled Rails application from its install directory (configuration templates live in `config/database.yml` and `config/smtp.yml` there); browse to `http://127.0.0.1:3000` and the project board is ready for the team.

### Run the official container image

```
docker run -it -p 3000:80 dradis/dradis-ce
```

Outcome: starts Dradis CE in an isolated container with the web interface forwarded to local port 3000 — convenient for a shared engagement VM that several testers reach over the network.

*No corpus evidence — derived from upstream documentation.*
