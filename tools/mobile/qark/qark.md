---
name: "qark"
categories:
  - blackarch-mobile
version: "301.ba1b265"
homepage: "https://github.com/linkedin/qark"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "linkedin/qark"
github-stars: 3397
github-pushed: "2024-01-16"
github-archived: false
github-issues: 79
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# qark

> Quick Android Review Kit: static review of APKs or Java source for common Android security issues.

## What it's for

QARK (Quick Android Review Kit, from LinkedIn) decompiles an APK — or reads
Java source directly — and checks it against a catalog of common Android
application weaknesses: inadvertently or improperly exported components,
interception-prone intents, weak or improper cryptography, embedded private
keys, risky WebView configurations, tapjacking, world-readable/writable files,
`debuggable` and backup-enabled flags, and outdated API levels. For several
findings it can go beyond reporting and generate ready-to-run ADB commands or
a proof-of-concept exploit APK; the issues it targets are exploitable without
rooting the test device. The upstream has been dormant since 2024 and its
packaging predates Python 3-only tooling, so treat it as a legacy but working
reviewer rather than an actively developed one.

## Typical use cases

### Review an APK

Run the full check catalog against a packaged app.

```
qark --apk target.apk
```

Outcome: decompiles the APK (leveraging multiple decompilers automatically),
runs every check, prints findings with educational explanations to the
console, and writes a JSON report; each finding includes where in the
manifest or code the issue lives.

### Review Java source

Skip decompilation when source is already available.

```
qark --java path/to/src
```

Outcome: accepts either a directory of Java files or a single `.java` file
(`--java path/to/File.java`) and runs the same check catalog against the
source tree, producing the same style of JSON report.

### Choose the report format and location

Get the findings in the shape the pipeline wants.

```
qark --apk target.apk --report-type html --report-path out/qark-report
```

Outcome: writes the report in the chosen format instead of default JSON —
`--report-type` accepts `html`, `xml`, `json` or `csv` — into the directory
given by `--report-path`; `--keep-report` preserves intermediate report data.

### Generate a proof-of-concept exploit APK

Turn a subset of findings into a demonstrable exploit.

```
qark --apk target.apk --exploit-apk
```

Outcome: for the vulnerability classes it knows how to exploit, emits ADB
commands and builds a deployable PoC APK (requires the Android SDK with
`platforms;android-21` and `build-tools;21.1.2`, pointed at with
`--sdk-path`; output lands under `--build-path`).

*No corpus evidence — derived from upstream documentation.*
