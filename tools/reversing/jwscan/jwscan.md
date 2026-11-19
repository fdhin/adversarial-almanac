---
name: "jwscan"
categories:
  - blackarch-reversing
version: "7.874b3a5"
homepage: "https://github.com/katjahahn/JWScan"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "katjahahn/JWScan"
github-stars: 37
github-pushed: "2015-06-16"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jwscan

> Detects and extracts Java archives embedded in Windows PE files by Jar-to-EXE wrapper tools.

## What it's for

JWScan (a Scala tool by Katja Hahn) examines a Windows PE executable and reports whether it was produced by a Jar-to-EXE wrapper such as Launch4j, Exe4j, JSmooth or Jar2Exe. It matches wrapper-specific signatures plus wrapper-independent indicators (embedded ZIP/jar structures, jar manifests, calls to `java.exe`/`javaw.exe`), prints the file offsets of any embedded archives, and can dump them so the original jar can be analyzed directly. It does not unpack general-purpose installers or native packers — only Java-in-EXE wrapping. On BlackArch the jar lives at `/usr/share/jwscan/jwscan.jar` behind a `jwscan` wrapper; upstream invocation is `java -jar jwscan.jar` with identical arguments.

## Typical use cases

### Scan a PE for wrapper signatures

Check whether a suspect executable is really a wrapped Java application.

```
jwscan target.exe
```

Outcome: prints a report listing the signatures found (for example a Launch4j signature, a jar manifest, calls to java.exe/javaw.exe) each labeled strong or weak, plus the offsets of embedded ZIP/Jar data — strong indicators confirm a wrapped jar and give you the offset to dump next.

### Dump the embedded archive at a reported offset

Extract the jar that the scan located, for decompilation with Java tooling.

```
jwscan -d 0x5c00 target.exe
```

Outcome: writes the embedded jar/zip found at offset `0x5c00` to disk, ready to open in a Java decompiler.

*No corpus evidence — derived from upstream documentation.*
