---
name: "uptux"
categories:
  - blackarch-scanner
version: "33.85ccfd0"
homepage: "https://github.com/initstring/uptux"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "initstring/uptux"
github-stars: 299
github-pushed: "2019-10-02"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# uptux

> Linux privilege-escalation checks for the modern userland — writable systemd units, over-permissive D-Bus services, and root HTTP APIs hiding on unix sockets.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding privilege-escalation vectors that the classic SUID/cron checklists walk past. uptux audits the parts of a Linux system that systemd and D-Bus introduced: writable systemd paths and unit files (services, timers, sockets), the executables and relative-path statements those units reference, broken symlinks pointing into writable directories, writable D-Bus paths, over-permissive D-Bus service settings, and HTTP APIs running as root on file-bound unix domain sockets. It is a single-file Python script (Python 2 and 3 compatible) with nothing to install — run it on the host being assessed. It enumerates and explains findings; it does not exploit them, and by the author's own note it does not replace the classic checks ("Don't skip the classics!").

## Typical use cases

### Enumerate modern-userland escalation vectors on a host

Run it as root so every path is visible:

```
sudo uptux
```

Outcome: prints categorized findings — writable systemd paths, units and socket files, writable D-Bus paths, root HTTP APIs on unix domain sockets — and writes a logfile of the run. Each reported path is a candidate misconfiguration to turn into an escalation by hand.

### Run the same checks without leaving a logfile

```
sudo uptux -n
```

Outcome: identical console output, but no logfile is written to disk — use when leaving artifacts on the assessed host matters. Add `-d` for extra debugging detail while triaging a specific finding.
