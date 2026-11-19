---
name: "androguard"
categories:
  - blackarch-binary
version: "3.3.5"
homepage: "https://github.com/androguard/androguard"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: APACHE
meta-checked: "2026-11-09"
github-repo: "androguard/androguard"
github-stars: 6409
github-pushed: "2026-10-25"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 29
gh-activity-checked: "2026-11-09"
---

# androguard

> Python toolkit and CLI for reverse-engineering Android apps: APK/DEX parsing, disassembly, decompilation and vulnerability scanning.

## What it's for

Androguard dissects Android applications statically: APK archive structure, decoded manifest (binary XML), DEX classes/methods/strings, Dalvik disassembly, Java decompilation, cross-references, method emulation and a vulnerability scanner validated against the OWASP MASTG demo set — plus optional native ARM64 analysis and APK decode/rebuild. Reach for it when you need to audit or triage an APK, from the CLI or from Python via the `Application` API and `AnalyzeAPK`. Mind the interface split: current upstream (v5) uses an option-style CLI (`androguard -i app.apk ...`), while older releases — 3.3.5, still what several distros package, and 4.1.4 on PyPI — use a subcommand CLI (`analyze`, `apkid`, `sign`, `cg`, `decompile`, `gui`) with different flags. Disassembly, decompilation, ARM and patching features require the matching optional extras.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Summarize an APK

Get package, main activity and code volume at a glance.

```
androguard -i app.apk
```

Outcome: prints a summary — app name, package, main activity, DEX files, class/method/string counts and signature status.

### List classes or methods

Enumerate the code surface of the package.

```
androguard -i app.apk --list-classes
androguard -i app.apk --list-methods
```

Outcome: prints class names (or method names) one per line; pipe through grep to narrow the listing.

### Disassemble selected methods with CFG

Zero in on specific code by class and method regex.

```
androguard -i app.apk --disasm --class TestActivity --method onCreate --cfg
```

Outcome: prints the Dalvik disassembly of matching methods with basic-block/CFG structure (requires the disasm extra); `--limit` caps the number of methods printed.

### Decompile to Java sources

Produce readable Java for review.

```
androguard -i app.apk -d decompiled/ --only-package com.example.app
```

Outcome: writes a decompiled Java package tree under `decompiled/`, restricted to the given package (requires the decompile extra).

### Hunt references to a value

Find where a string or type of interest is used.

```
androguard -i app.apk --findrefs string --findrefs-value password
```

Outcome: prints cross-references showing which classes and methods touch the searched value.

### Scan for known vulnerability classes

Run the static vulnerability scanner over the app.

```
androguard -i app.apk --scan-vulns
```

Outcome: prints detected issues labeled with MASVS/MASTG identifiers.
