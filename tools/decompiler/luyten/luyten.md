---
name: "luyten"
categories:
  - blackarch-decompiler
version: "0.5.4"
homepage: "https://github.com/deathmarine/Luyten"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-02-10"
build-system: scripts
license: APACHE
meta-checked: "2026-11-09"
github-repo: "deathmarine/Luyten"
github-stars: 5108
github-pushed: "2024-07-11"
github-archived: false
github-issues: 194
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# luyten

> Open-source desktop GUI for the Procyon Java decompiler.

## What it's for

Luyten wraps the Procyon decompiler engine in a swing-based desktop application for browsing compiled Java code. Point it at a JAR archive or `.class` file and it decompiles packages on demand, showing a tree of classes alongside syntax-highlighted, searchable source. It is a read-only viewer: it does not edit or re-export bytecode (use a bytecode editor such as Recaf for that). Reach for it when you want to read third-party Java code quickly without creating an IDE project, or when you want Procyon's output without the command line.

## Typical use cases

### Launch the decompiler GUI

Start the application with no arguments.

```
luyten
```

Outcome: opens the Luyten window; load a JAR or class file via File → Open File and browse its decompiled sources.

### Open an archive directly at startup

Pass the file to inspect as the first argument.

```
luyten target.jar
```

Outcome: the GUI starts with target.jar already loaded — the navigation tree lists its packages and classes, and selecting a class shows Procyon's decompiled Java source in the editor pane. Single `.class` files work the same way.

*No corpus evidence — derived from upstream documentation.*
