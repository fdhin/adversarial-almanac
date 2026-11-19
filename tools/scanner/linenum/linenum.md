---
name: "linenum"
categories:
  - blackarch-scanner
version: "75.c47f9b2"
homepage: "https://github.com/rebootuser/LinEnum"
packaged-by: [blackarch]
blackarch-builddate: "2020-01-08"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "rebootuser/LinEnum"
github-stars: 8082
github-pushed: "2023-09-06"
github-archived: false
github-issues: 25
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# linenum

> One-stop shell script for local Linux enumeration and privilege-escalation checks.

## What it's for

LinEnum automates the local enumeration you would otherwise type by hand after landing a shell on a Linux host: kernel and distribution details, users and password policy, sudo rules, cron jobs and systemd timers, SUID/SGID binaries and POSIX capabilities, world-writable files, interesting services, and Docker/LXC container detection. It does not exploit anything — it collects and triages, so you read its output and pick the escalation path. It is a single Bash script with no dependencies beyond coreutils, designed to be run on the target host itself (packaged on BlackArch as the `linenum` command, a symlink to `LinEnum.sh`).

## Typical use cases

### Quick enumeration sweep

Get the standard enumeration picture right after a foothold.

```
./LinEnum.sh
```

Outcome: prints a long sectioned report to the terminal (system, users, sudo, cron, SUID/SGID, world-writable files, services). Without options it stays in quick mode — fast checks only, no report file — so read the output top-down for anything that grants an escalation path.

### Thorough pass when quick mode finds nothing

Slow down and widen the search space.

```
./LinEnum.sh -t
```

Outcome: adds the lengthy checks (extended file and permission sweeps) to the same report — noticeably slower, but surfaces findings the quick scan skips.

### Save a report and export collected files

Keep the results for offline review.

```
./LinEnum.sh -t -r linenum-report.txt -e /tmp/linenum-export
```

Outcome: writes `linenum-report.txt` to the current directory and copies interesting files it collected into `/tmp/linenum-export` (created if missing), so the evidence survives after the session ends.

### Hunt a keyword across configs and logs

Find where a secret or service name appears without grepping by hand.

```
./LinEnum.sh -k passwd
```

Outcome: searches gathered `*.conf` and `*.log` content for the keyword and lists matches in a dedicated section of the report.
