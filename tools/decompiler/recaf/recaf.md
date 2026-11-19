---
name: "recaf"
categories:
  - blackarch-decompiler
version: "4.0.0.alpha.r594.gd5888cd"
homepage: "https://github.com/Col-E/Recaf"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-25"
build-system: java
license: MIT
meta-checked: "2026-11-09"
github-repo: "Col-E/Recaf"
github-stars: 7454
github-pushed: "2026-11-02"
github-archived: false
github-issues: 61
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 568
gh-activity-checked: "2026-11-09"
---

# recaf

> IDE-like Java and Android bytecode editor: decompile, search, edit, and re-export compiled applications.

## What it's for

Recaf opens compiled Java applications (JARs, class files, and Android APKs/DEX) in a familiar IDE-like interface. Browse and decompile classes with switchable engines (CFR, Fernflower, Procyon), search for strings, constants, and references, then edit code at two levels — directly in the decompiled source or through a bytecode assembler — and export the modified application. It also ships deobfuscation tooling (pattern-driven mass renaming with exportable mappings, constant-decryption and control-flow transformers) and defensively parses maliciously crafted class and JAR files that crash other tools. Use it when reading is not enough and you need to change bytecode without the original source. It is primarily a desktop application; automation goes through its script and plugin API, which can also run headless from the command line.

## Typical use cases

### Open an application in the editor

Load a target straight into the workspace.

```
java -jar recaf.jar --input app.jar
```

Outcome: launches the Recaf UI with app.jar already loaded as a workspace; classes appear in the navigation tree, ready to decompile, search, and edit without going through the file menu.

### Run an automation script without the GUI

Script a workflow and run it headless.

```
java -jar recaf.jar --headless --input app.jar --script script.java
```

Outcome: starts Recaf without a window, loads app.jar into a workspace, and executes script.java — a Recaf script with access to the same search, transformation, and export APIs as the UI. Add `--silent` (-q) to suppress log output on stdout.

### Keep a headless instance running for plugins

Host plugin-provided services in the background.

```
java -jar recaf.jar --headless --idle
```

Outcome: starts Recaf without the UI and keeps the process alive after startup, so plugin-hosted services (for example a remote automation endpoint) stay available instead of exiting once startup work completes.

*No corpus evidence — derived from upstream documentation.*
