---
name: "linux-smart-enumeration"
categories:
  - blackarch-scanner
version: "300.b83a26f"
homepage: "https://github.com/diego-treitos/linux-smart-enumeration"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-09"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "diego-treitos/linux-smart-enumeration"
github-stars: 4008
github-pushed: "2026-05-03"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# linux-smart-enumeration

> Graduated Linux enumeration that surfaces privilege-escalation findings by importance.

## What it's for

lse (Linux Smart Enumeration, upstream script `lse.sh`) collects the same class of local Linux data as other enumeration scripts — users, sudo, filesystem, cron, network, services, containers, known CVEs — but orders the output by privilege-escalation value: level 0 shows only highly important results, level 1 adds interesting ones, level 2 dumps everything gathered. While the tests run it can also watch the process list for recurring executions. It is a mostly-POSIX single shell script inspired by LinEnum, meant to be copied to and run on the host you are enumerating (installed on BlackArch as the `linux-smart-enumeration` command).

## Typical use cases

### Default importance-filtered run

See only what matters first.

```
linux-smart-enumeration -i
```

Outcome: runs every test but prints only highly important findings, marked with green `yes!` — the `-i` flag skips the interactive password questions the script otherwise asks.

### Escalate verbosity progressively

Widen the view only when the previous level gave no path.

```
linux-smart-enumeration -i -l 1
```

Outcome: adds interesting-but-not-critical findings to the report; `-l 2` dumps all gathered information (pipe through `less -r` for comfort). The intended workflow is 0 → 1 → 2.

### Run a single test or section

Re-run or skip parts of the enumeration.

```
linux-smart-enumeration -i -l 2 -s usr010,net,pro
```

Outcome: executes only test `usr010` plus the whole `net` and `pro` sections — the section codes (usr, sud, fst, sys, sec, ret, net, srv, pro, sof, ctn, cve) are listed in the help output.

### Watch for recurring process executions

Catch cron-like jobs that only appear periodically.

```
linux-smart-enumeration -i -p 120
```

Outcome: monitors the process list for two minutes while the other checks run and reports programs that re-executed during the window — often the quickest way to spot a recurring job worth hijacking.
