---
name: "iosforensic"
categories:
  - blackarch-forensic
version: "1.0"
homepage: "https://github.com/Flo354/iOSForensic"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "Flo354/iOSForensic"
github-stars: 65
github-pushed: "2014-06-10"
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

# iosforensic

> SSH-driven live acquisition helper for jailbroken iOS devices: pulls application files and logs, extracts SQLite databases and converts plists to XML.

## What it's for

Python tool by Florian Pradines (Phonesec), an OWASP-referenced iOS security project (2014). It connects over SSH (using sshpass) to a jailbroken device and copies out data for the application names you give it: application files, the syslog, SQLite databases it finds, and binary plists converted to XML. It targets the iOS 5–7 era and its upstream is archived, so expect it on old device images only. It works against a live device, not against an iTunes backup on disk — for backup parsing see [[ipba2]] or [[iphoneanalyzer]].

## Typical use cases

### Acquire one application's data from a device

Pull everything the tool can extract for a single app over default SSH.

```
iOSForensic.py -i 198.51.100.10 MyApp.app
```

Outcome: connects to the device as root over SSH on port 22 (default password `alpine`), then downloads the app's files, extracts SQLite databases and plist files converted to XML, and fetches the syslog.

### Acquire several apps with custom SSH settings

Use a non-default port and password, and match apps by partial name.

```
iOSForensic.py -i 198.51.100.10 -p 1337 -P Password1 MyApp.app angry MyApp2
```

Outcome: same extraction as above over the given port and credentials; `angry` matches any application whose name contains that string, so full names, partial names and multiple apps can be mixed in one run.

### Watch what the extractor is doing

Get progress detail during acquisition.

```
iOSForensic.py -v -i 198.51.100.10 MyApp.app
```

Outcome: verbose mode logs each transfer and conversion step as it happens.

*No corpus evidence — derived from upstream documentation.*
