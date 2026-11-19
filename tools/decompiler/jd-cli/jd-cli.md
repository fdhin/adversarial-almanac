---
name: "jd-cli"
categories:
  - blackarch-decompiler
version: "1.2.0"
homepage: "https://github.com/kwart/jd-cli"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-06-21"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "kwart/jd-cli"
github-stars: 655
github-pushed: "2026-11-03"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 57
gh-activity-checked: "2026-11-09"
---

# jd-cli

> Command-line Java decompiler — turn class files, JARs and WARs back into source trees, zips or console output.

## What it's for

jd-cli is a command-line wrapper around the JD-Core decompiler (the engine
behind JD-GUI). It accepts `.class` files, archives such as `.jar` and `.war`,
or directories, reconstructs the Java source, and writes it wherever you
point it: the console, a flat output directory, a directory structured by
package path, or a ZIP file. Reach for it to batch-recover source for a
compiled application during review — for example a WAR pulled off a server —
or to script decompilation into a build or triage pipeline. For interactive
browsing use its graphical counterpart, jd-gui.

## Typical use cases

### Decompile an archive into a source directory

Recover a browsable source tree from a compiled artifact.

```
jd-cli application.jar -od src/
```

Outcome: every class in `application.jar` is decompiled into `src/` (flat
layout); add `--skipResources` (`-sr`) to copy only classes, or `-n` to
include original line numbers as comments.

### Print matching classes to the console

Read one package's source quickly without writing files.

```
jd-cli -oc -p 'com/example/.*' application.jar
```

Outcome: only entries whose names match the regexp are processed and their
reconstructed source is streamed to stdout (`-oc` selects console output).

### Preserve package structure in the output tree

Mirror the original package layout instead of a flat dump.

```
jd-cli application.war -ods src-main/
```

Outcome: decompiled files land under `src-main/` along their package paths
(e.g. `src-main/com/example/Auth.java`), matching how the archive was built.

### Package decompiled source as a ZIP

Produce a source bundle ready to attach to an IDE or artifact.

```
jd-cli application.jar -oz application-src.zip
```

Outcome: all decompiled classes (and resources, unless skipped) are written
into `application-src.zip`.

*No corpus evidence — derived from upstream documentation.*
