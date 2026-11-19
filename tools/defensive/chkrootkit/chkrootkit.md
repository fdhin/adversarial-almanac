---
name: "chkrootkit"
categories:
  - blackarch-defensive
version: "0.59"
homepage: "http://www.chkrootkit.org/"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Magentron/chkrootkit"
github-stars: 260
github-pushed: "2022-11-15"
github-archived: true
github-issues: 11
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chkrootkit

> Classic locally-run rootkit detector: checks system binaries, log files and the kernel for signs of known rootkits, worms and LKM trojans.

## What it's for

chkrootkit is a shell script plus small C helpers that examines a Unix system for signs of an established rootkit: modified system binaries (ps, ls, netstat, sshd, and dozens more), interfaces in promiscuous mode, deletions in wtmp/lastlog/utmp, and hidden LKM trojans. It knows the signatures of 70+ rootkits, worms and LKMs (lrk3 through lrk6, t0rn, Adore, SucKIT, shv4/shv5, OSX.RSPLUG.A, Mumblehard, and from 0.59 the XZ backdoor and the Bottkitty UEFI bootkit). It is after-the-fact detection, not prevention, and it does not validate binaries against a package database — use file-integrity tools for that. Best run from trusted media (or with -p pointing at known-good binaries) against a suspect system.

## Typical use cases

### Run the full scan

As root, run every available test against the live system.

```
chkrootkit
```

Outcome: one result line per test (for example "checking `ps'... not infected", promiscuous-interface checks, wtmp/lastlog checks); any "INFECTED" verdict marks a binary or check that matched a rootkit signature and warrants imaging and offline analysis.

### Check only specific binaries or tests

Limit the run to the tests of interest.

```
chkrootkit ps ls sniffer
```

Outcome: only the trojan checks for ps and ls and the promiscuous-interface check run — a fast targeted check; -l lists every available test name.

### Scan a mounted suspect image offline

Check a compromised disk without executing its own binaries.

```
chkrootkit -r /mnt/suspect
```

Outcome: tests run with /mnt/suspect as the root directory, so paths like /mnt/suspect/usr/bin/ps are examined instead of the live system's; combine with -p pointing at trusted helper binaries so the image cannot feed chkrootkit trojaned commands.

### Expert mode for manual review

Inspect the suspicious strings inside system binaries yourself.

```
chkrootkit -x | more
```

Outcome: prints raw suspicious strings extracted from binaries and directories (hidden entries, odd paths) for manual analysis — expert mode surfaces evidence rather than verdicts; -q restricts output to infected results only.

*No corpus evidence — derived from upstream documentation.*
