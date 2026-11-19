---
name: "android-apktool"
categories:
  - blackarch-reversing
version: "2.7.0"
homepage: "https://github.com/iBotPeaches/Apktool/releases"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-02-25"
build-system: java
license: Apache
meta-checked: "2026-11-09"
github-repo: "iBotPeaches/Apktool"
github-stars: 27065
github-pushed: "2026-11-02"
github-archived: false
github-issues: 76
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 98
gh-activity-checked: "2026-11-09"
---

# android-apktool

> Apktool — decodes Android APKs to nearly-original resources plus smali, and rebuilds them after modification.

## What it's for

Apktool is the standard reverse-engineering tool for third-party, closed-source Android apps: it decodes an APK into a project-like tree — resources brought back to nearly original form (manifest, XML, assets, `res/`), the Dalvik bytecode disassembled into editable smali — and it rebuilds a working APK from that tree after you change things. Typical purposes are static analysis of app behavior, inspecting embedded configuration and endpoints, localizing apps, patching small behaviors for debugging, and unpacking/repacking as part of deeper analysis (often alongside jadx for readable Java). It does not decompile to Java source — that is jadx/jeb territory; smali editing is its level.

## Typical use cases

### Fully decode an APK

```
apktool d app.apk
```

Outcome: creates `app/` containing the decoded manifest, resources and smali for every dex; this is the starting point for reading embedded URLs, API keys and logic in static analysis.

### Decode resources only, skipping bytecode

```
apktool d -s app.apk -o app-resources
```

Outcome: decodes into `app-resources/` but `-s` skips the dex-to-smali disassembly, so you get the manifest, XML and assets quickly for a first pass at configuration and attack surface without waiting for full smali extraction (drop `-s` when you need the code).

### Rebuild a modified APK

```
apktool b app
```

Outcome: reassembles the decoded tree into an APK under `app/dist/`; the rebuild is unsigned, so sign it with `apksigner` before installing — the standard loop for patched test builds.
