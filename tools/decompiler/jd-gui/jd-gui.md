---
name: "jd-gui"
categories:
  - blackarch-decompiler
version: "1.6.6"
homepage: "https://github.com/java-decompiler/jd-gui"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2024-10-07"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "java-decompiler/jd-gui"
github-stars: 15227
github-pushed: "2024-07-08"
github-archived: false
github-issues: 248
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jd-gui

> Standalone GUI that reconstructs and displays Java source from CLASS files, JARs and WARs.

## What it's for

JD-GUI is the graphical front-end of the Java Decompiler project: give it a
`.class` file or an archive (`.jar`, `.war`, `.zip`) and it reconstructs the
Java source, presented in a package tree with instant navigation to methods
and fields. It is the quick way to read compiled code during review — for
instance a JAR or WAR recovered from a server when only the build artifacts
are available. Reconstructed sources can be exported from the File menu. For
headless, scripted decompilation of the same artifacts, use its command-line
counterpart jd-cli.

## Typical use cases

### Open a compiled archive for source browsing

Load a JAR or WAR and read its recovered source interactively.

```
jd-gui application.war
```

Outcome: the JD-GUI window opens with `application.war` loaded (the packaged
wrapper passes the argument to the jar; equivalently
`java -jar jd-gui.jar application.war`), showing a tree of reconstructed
classes — browse any class to read its decompiled methods and fields, then
use File > Save All Sources to export the whole set as a zip.
