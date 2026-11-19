---
name: "unix-privesc-check"
categories:
  - blackarch-automation
version: "1.4"
homepage: "http://pentestmonkey.net/tools/audit/unix-privesc-check"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2023-10-15"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "pentestmonkey/unix-privesc-check"
github-stars: 1082
github-pushed: "2021-02-07"
github-archived: false
github-issues: 27
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# unix-privesc-check

> Single-file shell script that audits a Unix host for misconfigurations allowing local users to escalate privileges.

## What it's for

unix-privesc-check (by pentestmonkey) is a standalone shell script you copy to a target and run there — no compilation, no dependencies beyond a POSIX shell. It checks file permissions, trusted paths, cron jobs, sudo configuration, world-writable files and similar misconfigurations that could let an unprivileged local user escalate to another user (often root) or reach local applications such as databases. It runs as either a normal user or root, and does a noticeably better job as root because it can read more files; it is a point-in-time configuration auditor, not an exploit tool. The upstream repo keeps two branches: `1_x` ships the classic fast single script `unix-privesc-check`, while `master` ships the slower but more thorough `upc.sh` with subdirectories — when in doubt, run both.

## Typical use cases

### Fast standard audit of the local host

```
./unix-privesc-check standard > upc-standard.txt
grep -c WARNING upc-standard.txt
```

Outcome: runs the speed-optimised checks and writes the full report to `upc-standard.txt`. Findings are marked with the word `WARNING`; a zero grep count means no misconfiguration was found.

### Deep detailed audit as root

```
sudo ./unix-privesc-check detailed > upc-detailed.txt 2>&1
grep WARNING upc-detailed.txt | sort -u
```

Outcome: runs the slower, more thorough check set with root-level readability of sensitive files, then extracts the unique findings to triage.

### Run the rewritten master-branch checker

```
./upc.sh > upc-master.txt
grep WARNING upc-master.txt
```

Outcome: runs the restructured checker (branch `master`), which takes longer but covers additional areas; compare its WARNING lines against the `1_x` run for full coverage.

*No corpus evidence — derived from upstream documentation.*
