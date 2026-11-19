---
name: "postenum"
categories:
  - blackarch-recon
version: "126.f91ba1c"
homepage: "https://github.com/mbahadou/postenum"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-21"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "mbahadou/postenum"
github-stars: 294
github-pushed: "2026-01-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# postenum

> Interactive post-exploitation console for Linux hosts that enumerates privilege-escalation opportunities across ten themed modules.

## What it's for

Postenum automates the system enumeration you perform after landing a shell on a Linux host: SUID/SGID binaries, sensitive configuration and database files, cron jobs and services, installed software versions, kernel-exploit candidates, shell escapes, and credentials stored in fstab or backup files. The packaged version (2.3) is an msfconsole-style interactive shell driven by `use`/`run`/`export` commands — older releases were a plain flag-driven script. It is read-only enumeration: it surfaces escalation leads, it does not exploit them. No arguments are needed; just launch it on the target.

## Typical use cases

### List the enumeration modules

See what the tool can check on this host.

```
postenum
show
```

Outcome: opens the Postenum console and prints the ten available modules with one-line descriptions (`sys_config`, `sys_info`, `sys_escape`, `sys_network`, `sys_cron`, `sys_soft`, `sys_db`, `sys_exploit`, `sys_user`, plus `allcheck`).

### Hunt SUID/SGID binaries and sensitive files

Run the classic privilege-escalation sweep.

```
use sys_config
run
```

Outcome: prints files with SUID/SGID bits set plus sensitive configuration and database file paths found on the host — each entry is a candidate for manual escalation review.

### Check for kernel exploits

Match the running kernel against known privilege-escalation exploits.

```
use sys_exploit
run
```

Outcome: reports the kernel version and any potentially applicable kernel privilege-escalation exploits, or advises manual searching when no accurate match exists.

### Run every module in one pass

Get the full enumeration picture without picking modules.

```
use allcheck
run
```

Outcome: executes all modules in sequence and prints their combined output — the fastest way to snapshot a freshly popped host.

### Save findings to a file

Export a module's output for the report.

```
use sys_user
run
export user-findings.txt
```

Outcome: after a module has run, writes its results to the named file in the current directory.

### Run a shell command without leaving the console

Spot-check a finding mid-session.

```
!id
```

Outcome: executes the system command (`id` here) and returns to the Postenum prompt; combine with `version`, `clear`, `reload` and `exit` for session control.

No corpus evidence — derived from upstream documentation.
