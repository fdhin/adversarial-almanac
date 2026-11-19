---
name: "reptor"
categories:
  - blackarch-misc
version: "0.7"
homepage: "https://github.com/Syslifters/reptor"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: custom:MIT
meta-checked: "2026-11-09"
github-repo: "Syslifters/reptor"
github-stars: 32
github-pushed: "2026-10-19"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 142
gh-activity-checked: "2026-11-09"
---

# reptor

> SysReptor's official CLI and Python library — turn tool output into pentest findings, notes and PDF reports without leaving the shell.

## What it's for

reptor automates pentest reporting against a SysReptor server: it pushes findings and notes (single or bulk), converts raw output from nmap, Nessus, OpenVAS, Qualys, Burp, ZAP and sslyze into findings, manages projects and finding templates, imports templates from DefectDojo or GhostWriter, and renders finished projects to PDF. Connection settings are the SysReptor URL and an API token, stored once with `reptor conf` or passed per command via `-s`, `-t` and `-p`; everything else is a subcommand, so it pipelines naturally with stdin. It requires a running SysReptor instance — it is not a standalone report writer — and it doubles as an importable Python library (`from reptor import Reptor`).

## Typical use cases

### Store the server URL and API token

Set up the connection once instead of passing flags every time.

```
reptor conf
```

Outcome: shows the current configuration and stores the SysReptor server, API token and default project; subsequent commands use the stored values.

### Push a quick note from stdin

Capture an observation into the project mid-testing.

```
echo 'Confirmed RCE on target.example.com' | reptor note
```

Outcome: uploads the text as a note to the configured project (timestamped by default; `-n` sets a custom title).

### Bulk-upload evidence files

Attach screenshots, dumps and logs to the report.

```
reptor file evidence/*
```

Outcome: uploads every file matching the glob to the current project as evidence attachments.

### Turn nmap XML into findings

Import scanner output directly instead of transcribing it.

```
reptor nmap --xml --upload -i nmap.xml
```

Outcome: parses `nmap.xml`, maps discovered services into SysReptor finding data and pushes them to the project; the same pattern works for `nessus`, `openvas`, `qualys`, `burp`, `zap` and `sslyze` (`--json --push-findings`).

### Export the project's findings

Pull the current finding set back out for review.

```
reptor exportfindings --format json
```

Outcome: writes the project's findings as JSON to stdout (other formats render a summary or checklist), suitable for diffing between reporting rounds.

*No corpus evidence — derived from upstream documentation.*
