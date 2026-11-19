---
name: "lazydroid"
categories:
  - blackarch-mobile
version: "25.0f559ec"
homepage: "https://github.com/nccgroup/LazyDroid"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-30"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "nccgroup/LazyDroid"
github-stars: 160
github-pushed: "2021-09-09"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lazydroid

> Menu-driven bash helper for the mechanical half of an Android assessment: manifest flag flips, rebuilds, re-signing, log and APK pulls, folder diffing and Frida Gadget injection.

## What it's for

LazyDroid (NCC Group, by Dani Martinez) automates the repetitive APK-reworking steps of an Android assessment: set an app's `debuggable` or `allowBackup` flag to true, rebuild with apktool, re-sign with your keystore, extract an installed app's APK from a device, filter logcat down to a single app's PID, download and diff snapshots of an app's data folder to see what it touched, and insert a Frida Gadget into the APK so non-rooted devices can still be instrumented. It is a workflow assistant, not a scanner: each menu entry drives adb, apktool, aapt and jarsigner underneath and needs those tools plus your own signing keystore configured. It is interactive only — no command-line flags, just a numbered menu.

## Typical use cases

### Open the task menu

Run the script from a checkout; the BlackArch package installs it as the `lazydroid` command.

```
./lazyDroid.sh
```

Outcome: prints a numbered menu (debuggable/allowBackup flip, build APK, sign APK, extract app log, extract installed APK, download a folder snapshot, compare two snapshots, insert Frida gadget); choose an entry and it runs the underlying apktool/adb/jarsigner steps, prompting for the APK or package it needs.

### Fetch the Frida gadget libraries for gadget injection

Get the per-architecture Android Frida libraries that the injection menu entry packs into APKs.

```
./getfridalibs.sh
```

Outcome: downloads the current Frida libraries for Android into the tool directory; afterwards the menu's Frida option auto-detects the connected device's architecture and inserts the matching gadget into the target APK, giving instrumentation on devices where a Frida server cannot be run.

*No corpus evidence — derived from upstream documentation.*
