---
name: "jok3r"
categories:
  - blackarch-webapp
version: "447.0761996"
homepage: "https://github.com/koutto/jok3r"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "koutto/jok3r"
github-stars: 1088
github-pushed: "2024-06-07"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jok3r

> Network-and-web pentest framework that chains 50+ third-party tools across a mission database.

## What it's for

Jok3r is a Python 3 CLI framework for network infrastructure and web black-box testing. Its premise: dozens of good open-source scanners already exist per service, so instead of rewriting them it orchestrates them — a managed toolbox of 50+ tools whose security checks are chained per target, selected adaptively by detected technologies, found credentials, and prior results. It fingerprints services (Wappalyzer-based for web), does CVE lookup against Vulners/CVE Details, brute-forces default credentials, attempts some auto-exploitation, and files everything into a per-mission SQLite database with an interactive `jok3rdb` shell and HTML report generation. Targets are managed as missions (workspaces) and can be imported from Nmap XML or Shodan. It is not a single-purpose scanner — it is the layer above them.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Inspect the managed toolbox

See which tools the framework can drive, and keep them current.

```
jok3r toolbox --show-all
```

Outcome: lists the 50+ bundled tools with their install/update status; follow up with `jok3r toolbox --update-all --auto` to refresh them without prompts.

### List supported services and their checks

Discover what jok3r can attack and which checks it will run.

```
jok3r info --services
jok3r info --checks http
```

Outcome: the first command prints every supported service (HTTP, FTP, SSH, SMB, MSSQL, MySQL, PostgreSQL, VNC, ...); the second prints the security checks available for HTTP and the tools that implement them.

### Attack a single web target interactively

Run all checks against one URL, filing results into a mission.

```
jok3r attack -t https://www.example.com/ --add2db mayhem
```

Outcome: fingerprinting runs first, checks are picked to match what was detected, and you are prompted at decision points; everything lands in the `mayhem` mission in the local database.

### Attack a network service non-interactively

Sweep a specific port/service pair in fast mode.

```
jok3r attack -t 198.51.100.10:1433 -s mssql --fast --add2db mayhem
```

Outcome: MS-SQL checks run against the target without any prompts (`--fast`), and results are stored under the `mayhem` mission; `--cat-only recon,vulnscan` narrows it to specific check categories.

### Hunt easy wins across a whole mission

Let the framework pick targets and profiles across everything registered so far.

```
jok3r attack -m mayhem --profile red-team --fast
```

Outcome: every service registered in the mission is attacked using the red-team attack profile, prioritizing critical and easily exploitable findings; `-f "service=ftp;port=21,2121"` filters to specific services or ports first.

### Review results and generate a report

Open the mission database shell.

```
jok3r db
```

Outcome: drops you into the `jok3rdb[...]>` shell, where `mission -a <name>` creates/switches missions, `nmap results.xml` and `shodan <ip>` import targets, `services`/`hosts`/`creds`/`vulns` summarize what was found, `results --search '<string>'` greps check output, and `report` generates the HTML report for the selected mission.
