---
name: "mobsf"
categories:
  - blackarch-mobile
version: "v4.5.3.r82.g0898efa"
homepage: "https://github.com/MobSF/Mobile-Security-Framework-MobSF"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "MobSF/Mobile-Security-Framework-MobSF"
github-stars: 22160
github-pushed: "2026-11-07"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 320
gh-activity-checked: "2026-11-09"
---

# mobsf

> All-in-one mobile application security platform: static, dynamic and web API analysis of Android and iOS apps through a web UI and a REST API.

## What it's for

MobSF (Mobile Security Framework, by Ajin Abraham and the MobSF organisation) is a self-hosted web application for mobile app security testing, penetration testing, malware analysis and privacy analysis. The static analyzer accepts APK, XAPK, AAB, JAR, AAR, DEX, SO, IPA, DYLIB, static-library and APPX binaries as well as zipped Android and iOS source code. The dynamic analyzer drives Android apps on Genymotion or Android Studio emulators, Corellium, or physical devices — rooted, or stock non-rooted from v4.6.0 via an injected Frida Gadget — with instrumented exploration and live web traffic capture; jailbroken iOS devices are supported from v4.5.1. A REST API (documented in-app at `/api_docs`) and helper scripts tie it into CI/CD pipelines. Historical note: v1.x releases shipped a `scripts/mobsfy.py` helper (later a "MobSFy Android Runtime" button in the web UI) that prepared rooted Android 4.03–4.4 runtimes by pushing a MITM CA and Xposed; both are gone from current releases, which auto-configure supported runtimes at analysis time, and neither a `mobsfy` script nor any `mobsfars` component exists in the current tree.

## Typical use cases

### Start the analysis web app

Launch the server that serves both the browser UI and the REST API.

```
./run.sh
```

Outcome: the first run performs setup, then the web app is served on `http://127.0.0.1:8000` and the REST API key is printed to the console — drag a binary into the static analyzer view, or open the Dynamic Analyzer page to work with a connected runtime.

### Batch static analysis over the REST API

Sweep a directory of binaries through an already-running MobSF instance.

```
python3 scripts/mass_static_analysis.py -s 127.0.0.1:8000 -k <rest_api_key> -d /path/to/binaries/
```

Outcome: uploads every mobile binary or zipped source tree in the directory and triggers a scan on each; completed scans show up under the server's recent scans and are retrievable via the REST API (`-r 1` forces a fresh rescan of already-seen files).

### Boot an emulator for dynamic analysis

Start a rooted Android VM that the dynamic analyzer can attach to.

```
scripts/start_avd.sh Pixel_5_API_30
```

Outcome: with no argument the script lists the available AVDs; with an AVD name it boots it (a start port and an OpenGApps zip can be passed as further arguments). Note the emulator serial from the output (e.g. `emulator-5554`) and export it as `MOBSF_ANALYZER_IDENTIFIER` when starting MobSF so the Dynamic Analyzer targets that runtime.

*No corpus evidence — derived from upstream documentation.*
