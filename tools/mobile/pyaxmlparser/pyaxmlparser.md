---
name: "pyaxmlparser"
categories:
  - blackarch-mobile
version: "v0.3.31.r13.g446bf7c"
homepage: "https://github.com/appknox/pyaxmlparser"
packaged-by: [blackarch, arch]
blackarch-builddate: "2026-01-13"
arch-builddate: "2025-12-14"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "appknox/pyaxmlparser"
github-stars: 150
github-pushed: "2026-04-21"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pyaxmlparser

> Parse Android binary XML (AXML) and read APK metadata from Python or the shell.

## What it's for

Android APKs store the manifest, layouts and resource strings as binary XML
(AXML), not plain text, so a text editor or `grep` alone cannot read them.
pyaxmlparser (by appknox) decodes that format and exposes the useful APK
metadata — app name, package name, version name/code, icon, and which
signature schemes (v1/v2/v3) the APK carries. It is used as a library by
other APK triage tooling and also ships a small `apkinfo` command for the
shell. It parses metadata and resources only — it does not decompile code or
look for vulnerabilities.

## Typical use cases

### Show APK metadata from the shell

Get package, version and signature facts for an APK without opening a decompiler.

```
apkinfo -s target.apk
```

Outcome: prints the app name, package name, version name and version code,
and boolean flags for v1/v2/v3 signature presence; `-s` (`--silent`) suppresses
debug and warning log lines so only the metadata block remains.

### Read APK metadata from Python

Pull the same fields programmatically, e.g. as a triage step in a larger
analysis script.

```
python3 -c "from pyaxmlparser import APK; apk = APK('target.apk'); print(apk.package, apk.version_name, apk.version_code)"
```

Outcome: the `APK` object parses the manifest on construction; `apk.package`,
`apk.version_name`, `apk.version_code`, `apk.application`, `apk.icon_info` and
`apk.icon_data` are then plain attributes, so the one-liner prints e.g.
`com.example.app 2.0.0 15`.

*No corpus evidence — derived from upstream documentation.*
