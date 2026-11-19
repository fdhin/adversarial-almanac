---
name: "loki-scanner"
categories:
  - blackarch-forensic
version: "1258.7688421"
homepage: "https://github.com/Neo23x0/Loki"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-21"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Neo23x0/Loki"
github-stars: 3801
github-pushed: "2026-01-12"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# loki-scanner

> YARA-based IOC and indicator scanner for triaging potentially compromised hosts.

## What it's for

Loki (Florian Roth) scans a system for indicators of compromise four ways: filename IOCs (regex on full paths), YARA rules against file data and process memory, MD5/SHA1/SHA256 hash checks, and C2 back-connect checks comparing process network endpoints against IOC lists. It is built for on-host triage — copy the program folder to the target and run it there; findings are reported as colored GREEN/YELLOW/RED lines that an analyst then investigates. Upstream has declared the Python version deprecated and in inactive maintenance in favor of THOR Lite (with a Rust rewrite in progress), but the scanner and its signature updater still work. Run it with administrator/root rights — without them some checks are disabled and protected files stay unread. On BlackArch the `loki-scanner` wrapper runs `loki.py` under python2.

## Typical use cases

### Scan a directory and keep the report

Basic filesystem IOC sweep with a log file.

```
loki-scanner -p /cases -l /cases/loki-report.txt
```

Outcome: walks the path checking filenames, hashes and YARA signatures; progress prints to the terminal while findings are appended to the log as ALERT/WARNING lines naming the matching indicator, ready for manual review.

### Intense scan

Trade speed for coverage.

```
loki-scanner -p /cases --intense
```

Outcome: also scans unknown file types and all extensions instead of the default relevance filter — slower, but catches artifacts the default mode skips.

### Machine-readable output

Feed results to another tool.

```
loki-scanner -p /cases --csv
```

Outcome: results are written in CSV format to STDOUT instead of the human-readable report, for piping into a SIEM or a post-processing script.

### File-only triage

Skip the process scan when only the filesystem matters.

```
loki-scanner -p /cases --noprocscan
```

Outcome: the filename, hash and YARA sweep runs unchanged, but process memory and process connections are not inspected — faster on loaded systems (the mirror flag is `--nofilescan`).

### Refresh the signature base

Update IOCs before scanning.

```
python2 loki-upgrader.py
```

Outcome: downloads the current signature sets (YARA rules, filename and hash IOCs) into the program folder; run it from the Loki program directory on a system with internet access before moving the scanner to the target host.

*No corpus evidence — derived from upstream documentation.*
