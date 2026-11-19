---
name: "fernflower"
categories:
  - blackarch-decompiler
version: "802.c03cae7a"
homepage: "https://github.com/fesh0r/fernflower"
packaged-by: [blackarch]
blackarch-builddate: "2025-08-23"
build-system: java
license: Apache
meta-checked: "2026-11-09"
github-repo: "fesh0r/fernflower"
github-stars: 4435
github-pushed: "2026-11-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 50
gh-activity-checked: "2026-11-09"
---

# fernflower

> JetBrains' analytical decompiler for Java, runnable standalone from the command line.

## What it's for

Turning compiled Java back into source: class files, zips and jars (scanned recursively) are decompiled to .java files in an output directory. Fernflower was originally developed by Stiver and has been maintained by JetBrains since 2014 — it is the decompiler engine bundled inside IntelliJ IDEA to display Java code when debugging or navigating class files. This note covers the standalone command-line jar; the fesh0r repository is the long-standing mirror that BlackArch packages, and the correct spelling is "Fernflower", one word. Community forks exist (ForgeFlower, and its descendant QuiltFlower) but are not packaged in BlackArch. Sources prefixed with `-e=` are treated as libraries: they are not decompiled, but they inform the analysis of relationships between classes — which especially improves renaming of obfuscated identifiers.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Decompile a jar to a source tree

Recover Java sources from a compiled artifact.

```
fernflower /home/user/app/application.jar /home/user/src-out/
```

Outcome: every class in the jar is decompiled and the .java files are written under /home/user/src-out/; source arguments may be .class, .zip or .jar files or directories, which are scanned recursively.

### Decompile with generic signatures and library context

Improve output fidelity by reconstructing generics and supplying dependency classes.

```
fernflower -dgs=1 -e=/home/user/app/libs/dependency.jar /home/user/app/application.jar /home/user/src-out/
```

Outcome: same as above, but generic signatures are reconstructed (`dgs=1`) and the classes in dependency.jar are used to resolve cross-class relationships instead of being decompiled.

### Rename obfuscated identifiers

Make decompiled, obfuscated code recompilable.

```
fernflower -ren=1 /home/user/app/obfuscated.jar /home/user/src-out/
```

Outcome: ambiguous or obfuscated element names (reserved words or names shorter than three characters) are renamed to unique `class_`/`method_`/`field_` plus a consecutive number, so the emitted sources compile without identifier conflicts.
