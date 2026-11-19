---
name: "vuls"
categories:
  - blackarch-scanner
version: "v0.35.1.r3.g0ba85f8"
homepage: "https://github.com/future-architect/vuls"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-11-12"
build-system: go
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "future-architect/vuls"
github-stars: 12330
github-pushed: "2026-11-06"
github-archived: false
github-issues: 101
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 178
gh-activity-checked: "2026-11-09"
---

# vuls

> Agentless vulnerability scanner for Linux and FreeBSD, driven by a TOML config and a scan/tui/report workflow.

## What it's for

Vuls scans servers for vulnerabilities in OS packages (and can also cover WordPress, pip, and language-ecosystem packages), matches them against CVE/OVAL/KEV data from companion dictionaries (go-cve-dictionary, goval-dictionary, gost, …), and reports what needs patching. It is agentless: targets are described in a `config.toml` under `[servers.<name>]` sections (SSH for remote hosts, `port = "local"` for the machine it runs on), and scan depth is chosen per server with `scanMode` values such as `fast`, `fast-root`, `deep` and `offline`. Results live in a SQLite database per scan, so you can revisit, diff and re-report old scans. Reach for it when you maintain a fleet and want a repeatable, scriptable patch-audit loop; it is a defensive auditing tool, not an exploit scanner.

## Typical use cases

### Validate the target configuration before scanning

```
vuls configtest
```

Outcome: reads `config.toml`, checks SSH connectivity, sudo settings and OS detection for every `[servers.*]` entry, and reports configuration problems — run this first; a configtest failure means the scan will fail on that host.

### Scan all configured servers

```
vuls scan
```

Outcome: connects to every target in `config.toml`, collects installed packages (and any enabled non-OS package scanning), matches them against the configured vulnerability dictionaries and stores the results in the per-scan SQLite database — no report is printed yet.

### Scan a subset of targets

```
vuls scan server1 server2
```

Outcome: same scan as above but restricted to the named `[servers.server1]` / `[servers.server2]` entries, useful when one host needs a re-scan after patching.

### Explore results interactively

```
vuls tui
```

Outcome: opens a terminal UI over the latest scan — drill down from vulnerable hosts to packages to CVE summaries; `vuls tui <scan-id>` (IDs come from `vuls history`) reopens an older scan.

### Produce a machine-readable or one-line report

```
vuls report -format-one-line-text
vuls report -format-list
```

Outcome: re-renders the stored scan without rescanning — `-format-one-line-text` gives one summary line per host (handy for cron jobs and chat alerts), `-format-list` a per-CVE list; `-format-full-text` and `-format-json` are also available for archives and tooling.

*No corpus evidence — derived from upstream documentation.*
