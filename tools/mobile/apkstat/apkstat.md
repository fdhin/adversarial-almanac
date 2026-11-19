---
name: "apkstat"
categories:
  - blackarch-mobile
version: "18.81cdad3"
homepage: "https://github.com/hexabin/APKStat"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hexabin/APKStat"
github-stars: 33
github-pushed: "2017-10-02"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# apkstat

> Decodes an APK with apktool and condenses the manifest, hardcoded IPs/domains and strings into a few text reports.

## What it's for

APKStat automates the first pass over an unknown APK. It shells out to apktool to decode the file, parses `AndroidManifest.xml`, and writes the manifest story into `report.txt`: requested permissions, every activity, service, receiver, provider and activity-alias (grouped by type), which activity is the launcher, and per-type counts. It then greps the decoded tree for hardcoded IPv4 addresses and URLs (`recoveredips.txt`, `recovereddomains.txt`, each entry annotated with the file it came from) and appends `strings` output of the raw APK to `strings.txt`. It is a Python 2-era script (2017) with no flags — run it from the directory holding the APK, since it invokes apktool with a relative path and writes all outputs to the current directory. apktool must be installed.

## Typical use cases

### First-pass triage of an APK

Get a single-screen summary of what an app declares and where it might phone home.

```
apkstat target.apk
```

Outcome: apktool decodes `target.apk` into a sibling directory, and the current directory gains `report.txt` (component/permission lists, counts, launcher activity), `recoveredips.txt` and `recovereddomains.txt` (hardcoded IPs/URLs with source file), and `strings.txt` — the orientation set for deciding where to dig deeper.

*No corpus evidence — derived from upstream documentation.*
