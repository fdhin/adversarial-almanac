---
name: "androick"
categories:
  - blackarch-mobile
version: "8.522cfb4"
homepage: "https://github.com/Flo354/Androick"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Flo354/Androick"
github-stars: 94
github-pushed: "2018-07-04"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# androick

> Python 2 forensics helper that automates pulling the APK, private data, SQLite databases, manifest, permissions and filtered logcat output of an app off a rooted Android device over adb.

## What it's for

Androick (Florian Pradines, an OWASP-referenced project since 2013) removes the repetitive pull-and-catalog work of Android app forensics: given one or more package names it drives adb against a rooted device to download the app's APK, its data directories, databases, manifest, declared permissions and logcat output. It is intended for forensic acquisition of a device you are authorized to examine. Requirements are steep by modern standards: a rooted device with USB debugging, `aapt`, `adb`, `hprof-conv` and `sqlite3` on the host, and Python 2 — the project has been archived since 2018, so expect to pin an older environment to run it. The BlackArch package installs it as the `androick` command.

### Find the package name

Locate installed apps by partial package name before extracting.

```
androick -f player
```

Outcome: lists the packages installed on the connected device whose names contain `player`; add `-v` for verbose output. Feed the exact package name to the extraction commands below.

### Pull everything for one or more apps

Extract all artifacts Androick knows about in one pass.

```
androick -A com.example.app
```

Outcome: downloads the APK, app data directories, databases, manifest, permissions and logs for `com.example.app` from the rooted device into the working directory; several package names can be given in the same command. When multiple devices are attached, select one with `androick -D <serial> <package>` (long form `--device`). Use `androick -a` for tool/version information.

### Selective extraction with a logcat keyword filter

Take only the artifacts you need and filter the captured logs.

```
androick -d -s -p -l --keyLogs="licence,token" com.example.app
```

Outcome: pulls only the data directories (`-d`), SQLite databases (`-s`) and permissions (`-p`), plus logcat output (`-l`) reduced to lines matching the comma-separated keywords. When extracting several apps in one run, give per-package keyword sets separated by `|`, e.g. `--keyLogs="licence|token,otp"`. A memory-dump option also exists upstream but mostly fails against production builds.

*No corpus evidence — derived from upstream documentation.*
