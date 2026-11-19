---
name: "bytecode-viewer"
categories:
  - blackarch-binary
version: "2.12"
homepage: "https://github.com/Konloch/bytecode-viewer"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2024-06-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Konloch/bytecode-viewer"
github-stars: 15704
github-pushed: "2026-10-30"
github-archived: false
github-issues: 103
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 19
gh-activity-checked: "2026-11-09"
---

# bytecode-viewer

> Java/Android reverse-engineering workbench: decompile, disassemble, and re-assemble JAR/APK/DEX bytecode with six bundled decompilers, in a GUI or headless.

## What it's for

Bytecode Viewer (BCV, by Konloch) loads Java class files, JARs, WARs, and Android artifacts (APK, DEX, XAPK, APKM) and renders them through up to three side-by-side panes backed by six bundled Java decompilers (CFR, FernFlower, JADX, JD-GUI, Krakatau, Procyon), bytecode disassemblers, two assemblers (Krakatau, Smali/BakSmali), and built-in dex-to-jar converters. Use it to read obfuscated Java or Android code quickly, cross-check one decompiler against another on the same class, edit and recompile classes, or run the identical decompilation headless in a pipeline via its command-line interface. It is a desktop GUI first; the CLI covers input, decompiler, and output selection, not interactive analysis. On BlackArch the `bytecode-viewer` command wraps the released jar.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Decompile an entire JAR or APK from the command line

```
bytecode-viewer -decompiler cfr -i sample.apk -o decompiled.zip -t all
```

Outcome: runs headless, decompiles every class in sample.apk with CFR, and writes a ZIP of .java sources to decompiled.zip; add -nowait to skip the pause after the CLI status messages in scripted pipelines.

### List the available decompilers

```
bytecode-viewer -list
```

Outcome: prints every decompiler and disassembler name that -decompiler accepts (Procyon is the default when the flag is omitted) — check the exact names here before scripting the headless mode.

### Browse a sample interactively and compare decompilers

```
bytecode-viewer
```

Outcome: opens the GUI; drag a JAR, APK, or DEX onto the window and decoding starts automatically, then assign two or three decompilers to the View panes to compare their output on the same class side by side.
