---
name: "jaadas"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "https://github.com/flankerhqd/JAADAS/"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-19"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "flankerhqd/JAADAS"
github-stars: 349
github-pushed: "2017-04-12"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jaadas

> JAADAS: Soot-powered static analysis for Android APKs — taint flows, API misuse and intent-crash bugs.

## What it's for

JAADAS ("Joint Advanced Defect assEsment for android applications", flankerhqd,
written 2014, repository now archived) is a research static-analysis framework for
Android applications built in Java/Scala on top of the Soot bytecode analysis
framework. It combines intra-procedure and inter-procedure analysis — most notably
taint-flow detection from intent inputs to sensitive APIs (e.g. a Parcelable read
from an intent flowing into `startActivity`), vulnerable-API misuse checks, and
local-denial-of-service (intent crash) detection — and merges multidex APKs so all
dex parts are analyzed together. Detection rules live in the shipped `config/`
directory (Soot source/sink files plus Groovy rule files) and can be customized.
Two modes trade speed for depth: `--fastanalysis` disables inter-procedure analysis
to finish in under a minute for batch triage, while the default full mode can need
16 GB+ of heap for large apps. It requires a JDK and the Android SDK's `platforms/`
directory, with the `android.jar` matching the APK's targetSdk version present (a
symlink to a nearby version satisfies Soot). As a 2014 research prototype it knows
nothing of modern Android runtime changes — treat findings as leads, not verdicts.

## Typical use cases

### Fast triage of an APK

```
jaadas vulnanalysis -f app.apk -p /opt/android-sdk/platforms/ -c /usr/share/jaadas/config/ --fastanalysis
```

Outcome: Runs the intra-procedure-only analysis (usually under a minute), printing detected issues — taint flows, API misuse, intent crash paths — and writing the full JSON result to `output/<md5-of-apk>.txt` for later diffing.

### Full inter-procedure analysis

```
java -Xmx8192m -jar /usr/share/jaadas/jade-0.1.jar vulnanalysis -f app.apk -p /opt/android-sdk/platforms/ -c /usr/share/jaadas/config/
```

Outcome: Unleashes whole-application and inter-procedure dataflow analysis (omit `--fastanalysis`), which can take a long time and large heap — hence the `-Xmx` — but reports cross-method taint paths the fast mode misses, each with source and destination statements in the JSON output.

*No corpus evidence — derived from upstream documentation (README usage and packaged launcher `java -jar jade-0.1.jar` in /usr/share/jaadas).*
