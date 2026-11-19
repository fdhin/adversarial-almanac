---
name: "wnmap"
categories:
  - blackarch-automation
version: "0.1"
homepage: "http://nullsecurity.net/tools/automation.html"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-19"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# wnmap

> Modular nmap wrapper that chains user-defined scan modes against one host and files every result into a per-mode directory tree.

## What it's for

wnmap (nrz / nullsecurity.net) is a bash wrapper around nmap for host audits: you define "modes" — each a short flag mapped to a set of nmap arguments — in the `wmodes.conf` file, and wnmap runs one, several, or all of them against a single hostname or IP, storing every run's normal and XML output under a tidy `<target>/<mode>/` directory tree (`fast/`, `os/`, `version/`, … each with `target.nmap` + `target.xml`). That per-mode layout is the point: you get consistent, report-ready nmap artefacts instead of ad-hoc output files. It intentionally does not scan network ranges (nullsecurity's wscan covers that); wnmap is for depth-first scanning of one host. Note the packaged release dates from 2013 — it is a stable, simple script, but expect vintage nmap defaults in the sample config.

## Typical use cases

### Run every configured scan mode against a host

```
wnmap -C target.example.com
```

Outcome: chains all modes defined in `wmodes.conf` (sample file ships fast/ack/version/os/script modes), creating `target.example.com/<mode>/` directories containing the `.nmap` and `.xml` output of each scan.

### Run a single mode and force a rescan

```
wnmap -F 198.51.100.10 -r
```

Outcome: runs only the `-F` mode (fast scan in the sample config) against the host; `-r` makes wnmap rescan even if output for that target/mode already exists.

### Add a custom scan mode on the fly

```
wnmap -a "-I;idle-scan;-P0 -p- -sI target.example.com;true"
```

Outcome: appends a new mode to `wmodes.conf` — fields are `wnmap_arg;mode_name;nmap_args;needs_sudo` — after which `-I` is runnable like any built-in mode and its results get an `idle-scan/` directory.

### Edit the mode definitions

```
wnmap -e
```

Outcome: opens `wmodes.conf` in `$EDITOR` (vim by default) so you can add, remove or reorder scan modes permanently.

*No corpus evidence — derived from upstream documentation.*
