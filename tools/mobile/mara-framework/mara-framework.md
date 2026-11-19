---
name: "mara-framework"
categories:
  - blackarch-mobile
version: "176.ac4ac88"
homepage: "https://github.com/xtiankisutsa/MARA_Framework"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "xtiankisutsa/MARA_Framework"
github-stars: 673
github-pushed: "2019-07-26"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mara-framework

> Single-command reverse-engineering and analysis pipeline for Android APK, DEX, JAR and class files, chaining the standard mobile RE toolchain.

## What it's for

MARA (Mobile Application Reverse engineering and Analysis Framework, by Christian Kisutsa and Chrispus Kamau) wraps the common mobile analysis tools — baksmali, enjarify, jadx, smalisca, AndroBugs, androwarn, APKiD, ClassyShark, pyssltest, testssl, whatweb — into one bash pipeline run against an APK, DEX, JAR or .class file. It disassembles and decompiles the sample, mines the manifest (intents, exported activities/receivers/services, debuggable and backup flags), extracts strings, permissions, certificates, URLs, IP addresses and emails, runs vulnerability and malicious-behaviour scans oriented at the OWASP mobile top 10, and SSL-scans any domains found in the sample. Reach for it when you want a broad structured first pass over an app without wiring the tools together yourself. Development stopped in 2019, so the bundled tool versions lag well behind current releases.

## Typical use cases

### Analyze a single APK

Run the full pipeline against one app.

```
./mara.sh -s /path/to/target.apk
```

Outcome: banner, then staged progress output; everything lands under `data/target.apk/` — decompiled Java source, smali, unzipped resources, and static findings under `analysis/static/` (vulnerabilities from AndroBugs, malicious-activity notes from androwarn) — plus `data/domain_scans/` for the SSL and web scans of any domains discovered.

### Batch-analyze a directory of samples

Sweep a whole folder of APKs, DEX files or JARs in one run.

```
./mara.sh -m /path/to/apks/
```

Outcome: loops the same pipeline over each APK in the directory, producing one `data/<sample>/` workspace per file; `-x` and `-r` do the same for directories of DEX and JAR files.

### Analyze bytecode containers directly

Point the pipeline at unpacked build artifacts instead of a packaged app.

```
./mara.sh -d classes.dex
```

Outcome: the same decompile-and-analyze stages against the DEX (`-j` for a JAR, `-c` for a single compiled .class file), with output structured identically under `data/`.

*No corpus evidence — derived from upstream documentation.*
