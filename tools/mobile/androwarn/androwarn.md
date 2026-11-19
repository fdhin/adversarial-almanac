---
name: "androwarn"
categories:
  - blackarch-mobile
version: "135.626c02d"
homepage: "https://github.com/maaaaz/androwarn"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "maaaaz/androwarn"
github-stars: 537
github-pushed: "2020-01-21"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# androwarn

> Static analyzer that flags potentially malicious behaviours in an Android app from its Dalvik bytecode and writes an HTML, JSON or text report.

## What it's for

Androwarn disassembles an APK's Dalvik bytecode to Smali (via androguard) and runs structural and data-flow analysis against a dozen behaviour categories: telephony identifier exfiltration (IMEI, IMSI, cell IDs), device settings and log exfiltration, geolocation leakage, WiFi/Bluetooth credential theft, premium SMS and phone-call abuse, audio/video interception, remote connection establishment, PIM data (contacts, calendar, SMS, clipboard) access and modification, SD-card operations, arbitrary code execution, and denial of service. Findings are written to a report at one of three verbosity levels. It reports code-level *potential*, not verdicts — a legitimate mapping app will still be flagged for geolocation access, so treat categories as review leads.

## Typical use cases

### Generate an expert HTML report

Produce a standalone report with full Smali-level evidence for documentation or hand-off.

```
androwarn -i target.apk -r html -v 3 -o report.html
```

Outcome: analyzes `target.apk` and writes a self-contained HTML report (CSS/JS inlined) to `report.html`, organized by behaviour category; `-v 3` selects expert detail (`-v 1` essential, `-v 2` advanced). Without `-o`, the report lands in the current directory as `<package>_<timestamp>.html`.

### Emit a machine-readable JSON report

Get findings in a form pipelines can consume.

```
androwarn -i target.apk -r json -v 2
```

Outcome: runs the same analysis and writes the report as JSON (default filename `<package>_<timestamp>.json`); `-r txt` selects plaintext instead.

### Quick stdout triage

See the essential findings immediately without leaving the terminal.

```
androwarn -i target.apk -r txt -v 1 -d
```

Outcome: prints the essential-level analysis to stdout (`-d`/`--display-report`) in addition to writing the report file — a fast first look during triage. Add `-w` to also look the package up on Google Play during analysis.

*No corpus evidence — derived from upstream documentation.*
