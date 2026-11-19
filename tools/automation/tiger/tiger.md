---
name: "tiger"
categories:
  - blackarch-automation
version: "3.2.3"
homepage: "http://www.nongnu.org/tiger/"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2022-06-01"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "gitGNU/gnu_tiger"
github-stars: 5
github-pushed: "2017-05-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tiger

> UNIX host security auditor and host-based intrusion detection checker in pure shell.

## What it's for

Tiger (the Savannah/nonGNU project, not the unrelated archived GitHub
repository of the same name) audits a UNIX system's local security
posture. It is a framework of shell modules that check accounts, inetd and
service configuration, NFS exports, cron entries, anonymous FTP, path and
file permission problems and similar known misconfigurations, then writes
a report of findings. Run on a schedule it doubles as a host-IDS: `-G`
records MD5 signatures and permissions of system binaries so later runs
flag tampering. It complements rather than replaces file-integrity checkers
(tripwire, aide) and rootkit hunters (chkrootkit) — Tiger's own ground is
system configuration, and it can also drive those tools. It runs on the
host being audited; it is not a network scanner. Message IDs in the report
can be expanded with the bundled `tigexp` explainer.

## Typical use cases

### Run a full local security audit

```
tiger
```

Outcome: every module enabled in the `tigerrc` configuration runs and a
report named `security.report.<hostname>.<date>.<time>` is written to the
log directory (`/var/log/tiger` on packaged installs), one line per
finding with a message ID to look up via `tigexp`.

### Get the findings explained

```
tiger -E
```

Outcome: a second report, `explain.report.<host>.<date>.<time>`, is
written containing the explanation for each distinct finding type once;
use `-e` instead to splice explanations inline after every message, at the
cost of a much larger report.

### Quiet run for cron jobs

```
tiger -q
```

Outcome: suppresses progress and informational output so only the security
messages themselves are printed — the right shape for scheduled,
log-collected runs.

### Baseline system binaries for intrusion detection

```
tiger -G
```

Outcome: generates MD5 hashes and file permission signatures for system
binaries; subsequent tiger runs compare live state against them and report
modified binaries — the host-IDS half of the tool.

### Collect reports on a central log server

```
tiger -l @logserver.example.com
```

Outcome: instead of writing the report locally, tiger ships it to a Tiger
logging server listening on TCP 5353 at `logserver.example.com`, letting
many audited hosts report to one collector.

*No corpus evidence — derived from upstream documentation.*

GitHub: third-party mirror of the Savannah (nongnu.org) tiger source.
