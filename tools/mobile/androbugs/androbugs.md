---
name: "androbugs"
categories:
  - blackarch-mobile
version: "7fd3a2c.r0.g7fd3a2c"
homepage: "https://github.com/AndroBugs/AndroBugs_Framework"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "AndroBugs/AndroBugs_Framework"
github-stars: 1226
github-pushed: "2019-04-24"
github-archived: true
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# androbugs

> Python 2 static scanner that matches a disassembled Android APK against a database of known vulnerability "vectors" and ranks findings by severity.

## What it's for

AndroBugs Framework (Yu-Cheng Lin) statically analyzes Android apps for security vulnerabilities: it disassembles the APK and checks code and manifest against a set of analysis vectors — WebView misconfigurations, dangerous shell commands, weak crypto usage, missing best practices — reporting each finding grouped by severity level (Critical/Warning/Info). It also ships companion scripts for "massive analysis" of whole APK directories backed by MongoDB, and a `<Hacker>`-marked check of an app's anti-repackaging protections. It is Python 2.7-only and archived since 2019, so it reflects the Android app ecosystem of that era; pair it with a maintained scanner for current apps. On BlackArch the single-APK scanner is installed as the `androbugs` command and all scripts live under `/usr/share/androbugs/`.

### Scan a single APK

Run the default one-shot analysis against one app.

```
androbugs -f example.apk
```

Outcome: prints every analysis vector triggered by the app, grouped by severity level; upstream advertises an average scan time under two minutes. Upstream invocation without the distro wrapper: `python androbugs.py -f example.apk`.

### Bulk-scan a directory of APKs

Batch analysis with a batch number and tag so results can be queried later.

```
python2 /usr/share/androbugs/AndroBugs_MassiveAnalysis.py -b 20260101 -t appstore -d ./apks/ -o ./reports/
```

Outcome: scans every APK under `./apks/`, stores per-app vector results in the configured MongoDB instance and writes report files to `./reports/`. Requires a running MongoDB with PyMongo and the connection configured in `/usr/share/androbugs/androbugs-db.cfg` before the run.

### Query bulk results by vulnerability vector

List which scanned apps hit a specific vector at a given severity.

```
python2 /usr/share/androbugs/AndroBugs_ReportByVectorKey.py -v WEBVIEW_RCE -l Critical -b 20260101 -t appstore
```

Outcome: prints the apps from batch `20260101` (tag `appstore`) that triggered the `WEBVIEW_RCE` vector at Critical severity; append `-a` to include lower severities. For a summary across all vectors run `AndroBugs_ReportSummary.py -m massive -b 20260101 -t appstore` from the same directory.

*No corpus evidence — derived from upstream documentation.*
