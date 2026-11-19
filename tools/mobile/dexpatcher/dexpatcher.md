---
name: "dexpatcher"
categories:
  - blackarch-mobile
version: "1.7.0"
homepage: "https://github.com/DexPatcher/dexpatcher-tool"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-12-24"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "DexPatcher/dexpatcher-tool"
github-stars: 443
github-pushed: "2020-05-13"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dexpatcher

> Patch Android DEX/APK bytecode with patches written in Java instead of smali.

## What it's for

DexPatcher is a Dalvik bytecode patcher: it takes a source DEX or APK plus one
or more patch DEX files and writes the patched output. The intended workflow is
to decompile an APK with a DexPatcher-aware decompiler (Jadx), edit or extend
the Java source using DexPatcher annotations, compile only that code into a
patch DEX, and let DexPatcher merge it back into the original app — so patches
are written and maintained in Java rather than hand-edited smali. It is the
merge step of a toolchain, not a decompiler or builder; a separate Gradle
plugin automates the same flow inside Android Studio builds. Written by
Lanchon, GPLv3+.

## Typical use cases

### Apply a patch DEX to an APK

Merge a compiled patch into the original app package.

```
dexpatcher -o target-patched.apk target.apk patch.apk
```

Outcome: writes `target-patched.apk` with every patched class substituted into
the source DEX; `-o` may also name a directory for plain DEX output.

### Patch a multi-dex app

Handle source and patch sets that span several `classes*.dex` files.

```
dexpatcher -m -o patched-app/ source-app/ patch-app/
```

Outcome: enables multi-dex support, merges each corresponding DEX pair found
under the input directories, and writes the patched set under `patched-app/`.

### Trial-run a patch without writing output

Check that a patch applies cleanly before committing to an output file.

```
dexpatcher --dry-run -v target.apk patch.apk
```

Outcome: processes the patch and prints verbose diagnostics without writing any
files — use it to catch mismatched source references before generating output.

*No corpus evidence — derived from upstream documentation.*
