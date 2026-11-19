---
name: "rej"
categories:
  - blackarch-decompiler
version: "0.7"
homepage: "https://sourceforge.net/projects/rejava/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-10-03"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "huyphan/reJ"
github-stars: 6
github-pushed: "2013-09-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rej

> Java classfile workbench: a Swing GUI (plus a reusable Java API) for browsing, comparing, editing and obfuscating compiled `.class` files without the original source.

*No corpus evidence — derived from upstream documentation.*

## What it's for

reJ (the "reJava" project) works directly on compiled Java class files. Where a decompiler guesses back to source, reJ opens the classfile structure itself: constant pool, fields, methods and bytecode, presented in tabbed views (Files, Structure, Constant Pool, Editor, Injection, Obfuscation, Search, Compare, Debug, Hex). You can edit bytecode through a structured editor instead of a hex dump, search across class files, compare two classes side by side, and step through bytecode in the debug view. It is a graphical Java/Swing application, not a batch CLI — the packaged command only opens the workbench, and it needs a display (it exits with a headless exception on a terminal-only host). The same functionality is exposed as a Java API (`net.sf.rej.*`) for scripting. It does not decompile to Java source; for that reach for a decompiler such as Procyon or Fernflower.

## Typical use cases

### Open the classfile workbench

```
rej
```

Outcome: launches the reJ Swing window (the packaged `rej` wrapper runs `java -jar /usr/share/rej/rej.jar`). From the File menu, open a project or a single `.class` file to inspect its constant pool, fields, methods and bytecode; the Compare, Search and Obfuscation tabs operate on whatever is open. Requires an X/Wayland display.

### Run the jar directly against a class file location

```
java -jar rej.jar
```

Outcome: equivalent to the packaged wrapper when the jar is on hand outside a package install (e.g. downloaded from the project page). The GUI starts and the target class file is then opened from inside the application; file paths are not accepted as command-line arguments — the v0.7 entry point (`net.sf.rej.gui.MainWindow`) opens the window unconditionally and takes no options.
