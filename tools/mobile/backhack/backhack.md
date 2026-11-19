---
name: "backhack"
categories:
  - blackarch-mobile
version: "39.561ec86"
homepage: "https://github.com/l0gan/backHack"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "l0gan/backHack"
github-stars: 67
github-pushed: "2020-07-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# backhack

> Pull Android apps apart over adb backup — extract, inspect, modify and restore an app's private files without root.

## What it's for

backHack (by Kirk Hayes, l0gan) automates the adb-backup technique for app analysis: it runs `adb backup` against a chosen package, unpacks the resulting archive, and lets you inspect and modify the app's file-system contents (preferences, databases) before repacking and restoring it to the device. Since v3.0 it can also parse existing iTunes backups for iOS apps, and since v3.1 it can pull an app's APK directly. It requires adb on PATH, is a Python 2 era tool (encrypted backups additionally need the Java JCE unlimited-strength policy files, and Google Pixel devices force backup encryption), and its interactive menu mode offers the same operations as the command-line flags.

## Typical use cases

### List installed apps

```
python backHack.py --listapps
```

Outcome: prints the packages installed on the connected device, from which an exact `--app` target can be chosen.

### Back up and extract an app's private data

```
python backHack.py --app com.example.app
```

Outcome: runs an adb backup of the package, decompresses it, and extracts the app's file-system contents locally for inspection or modification.

### Pull an app's APK

```
python backHack.py --app com.example.app --apk
```

Outcome: downloads the app's APK from the device and saves it under the app name entered, ready for static analysis.

### Parse an iOS iTunes backup

```
python backHack.py --ios --app ExampleApp
```

Outcome: parses the iTunes backups stored on the computer for the named app and surfaces the files likely to be of interest (credentials, databases, plists).

*No corpus evidence — derived from upstream documentation.*
