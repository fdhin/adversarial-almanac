---
name: "dex2jar"
categories:
  - blackarch-hardware
version: "2.1"
homepage: "https://github.com/pxb1988/dex2jar"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2018-10-14"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "pxb1988/dex2jar"
github-stars: 13155
github-pushed: "2024-07-21"
github-archived: false
github-issues: 379
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dex2jar

> Converts Android's Dalvik Executable (.dex, inside APK/DEX files) into Java .class files zipped as a JAR, so Java tooling can decompile Android apps.

## What it's for

Bridging the gap between Android bytecode and the Java ecosystem. Decompilers such as JD-GUI and CFR read .class files, not .dex — dex2jar's `d2j-dex2jar` turns the `classes.dex` extracted from an APK into a normal JAR that those tools open directly, which is the classic first step of static Android app analysis. The distribution also bundles dex↔smali disassembly/assembling (`d2j-baksmali`, `d2j-smali`), string-decryption helpers (`d2j-decrypt-string`) and a dex reader/writer library. It does not decompile to Java source itself and does not re-sign APKs — pair it with a decompiler for reading and with apktool-style tooling for resource work. The old Google Code/SourceForge pages are dead; the GitHub repository is the canonical upstream.

## Typical use cases

### Convert an APK to a JAR for Java decompilers

```
d2j-dex2jar.sh -f target.apk
```

Outcome: writes `target-dex2jar.jar` next to the input (the `-f` flag overwrites an existing output). The JAR contains the app's classes as ordinary .class files — open it in JD-GUI, CFR or any Java decompiler to browse reconstructed Java source. On distro-packaged installs the wrapper is usually on PATH as `d2j-dex2jar`.

### Convert an extracted classes.dex

When only the raw dex was pulled out (from a device, another tool, or an unpacked APK):

```
d2j-dex2jar.sh classes.dex
```

Outcome: produces `classes-dex2jar.jar` from the bare dex file, same as the APK case — useful when the dex was recovered from `/data/app` or from an OTA/image rather than from a complete APK.

### Disassemble dex to smali for patching

```
d2j-baksmali.sh classes.dex -o smali_out
```

Outcome: the dex is disassembled into `smali_out/` as editable .smali files (one per class). Edit them, reassemble with `d2j-smali` back into a dex, and rebuild the APK — the standard loop for patching a check out of an app before dynamic analysis.

*No corpus evidence — derived from upstream documentation.*
