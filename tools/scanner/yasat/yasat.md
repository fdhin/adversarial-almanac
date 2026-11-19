---
name: "yasat"
categories:
  - blackarch-scanner
version: "848"
homepage: "https://yasat.sourceforge.net/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-10-07"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "montjoie/yasat"
github-stars: 23
github-pushed: "2024-10-13"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# yasat

> Yet Another Stupid Audit Tool — a self-contained shell-script auditor of local hardening on Linux/Unix hosts.

## What it's for

YASAT (Corentin Labbé, 2008–2015) audits the machine it runs on for configuration weaknesses: Apache, PHP, Bind, MySQL, Samba, Squid, OpenVPN, SNMP, CUPS, Tomcat, vsftpd, xinetd, syslog, network settings, kernel options, SUID binaries, package updates and user accounting, each checked by a plugin. Its design goals are minimal dependencies (a shell plus sed, grep and cut) and heavily documented findings — every test links to the official documentation explaining the issue. Results are color-coded findings plus per-plugin advice, optionally exported to HTML, with desktop and server profiles and NSA/CCE compliance views. It is a defensive host-audit tool in the family of lynis and tiger: it reads configuration, it does not attack anything, and it expects to run with enough privileges to read the configs it checks.

## Typical use cases

### Run a standard audit

```
yasat -s
```

Outcome: executes the standard plugin set against the local configuration files, pausing after each plugin (disable with `-a`) and printing color-coded findings — info, warning (orange), error (red) — each with an explanation and doc link.

### List available checks before auditing

```
yasat -l
```

Outcome: prints the plugin table (the same set covered by `--listtest`) without running any test — the quick way to see what this YASAT build covers on this OS.

### Export an HTML report

```
yasat -s -H --html-output /tmp/yasat-report.html
```

Outcome: runs the standard audit and additionally writes the HTML report to the given path (default `~/yasat/yasat.html`) — the shareable artifact for a hardening review.

### Trim the audit to relevant checks

```
yasat -s --skip nfs,ntp -f --print-level 2
```

Outcome: skips named plugins (comma-separated, without the `.test` suffix) — e.g. services the host does not run — while `-f`/`--full-scan` adds the slower extra tests and `--print-level 2` limits output to warnings and above.

### Audit against a compliance baseline

```
yasat -s --compliance all --server
```

Outcome: annotates findings with NSA/CCE compliance references (`cce`, `nsa` or `all`) and applies the server profile (use `--desktop` on workstations) — framing the same checks as a compliance delta instead of generic advice.

*No corpus evidence — derived from upstream documentation.*
